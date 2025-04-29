# Backend Structure Document

This document outlines the backend setup for the Relaxing App. It is written in plain language so that anyone can understand how the backend works, what technologies are used, and how everything fits together.

## 1. Backend Architecture

### Overview
- We rely on Supabase as a Backend-as-a-Service (BaaS). Supabase provides three main building blocks: authentication, object storage, and a PostgreSQL database.
- The mobile app communicates directly with Supabase via its RESTful API and client libraries.

### Design Patterns & Frameworks
- **Client-driven architecture**: The iOS app (Swift/SwiftUI) handles UI, state management (MVVM), and most business logic.  
- **Serverless components**: No custom servers. Supabase handles hosting, scaling, and updates.  
- **Row-level security**: We enforce data access rules in PostgreSQL so each user sees only their data (e.g., their custom mixes).

### Scalability, Maintainability & Performance
- **Auto-scaling**: Supabase automatically scales the database and storage based on demand.  
- **Managed upgrades**: Supabase handles database engine upgrades and security patches.  
- **Caching & CDN**: Supabase Storage integrates with a CDN to cache audio files close to end users.

## 2. Database Management

### Technologies Used
- **PostgreSQL** (relational, SQL database) hosted and managed by Supabase.  
- **Supabase Storage** (object storage) for audio files.  
- **Supabase Auth** for user authentication and identity management.

### Data Structure & Access
- **Sounds catalog**: Metadata (name, file path, tags) stored in a `sounds` table. Actual audio files stored in Supabase Storage.
- **User mixes**: Each custom mix is stored in a `mixes` table, with a JSON column describing the tracks and their settings.
- **Access patterns**:
  - App fetches the sounds catalog (public data).  
  - Authenticated requests fetch, insert, update, or delete a user’s mixes.  
  - Audio streaming/download uses signed URLs from Supabase Storage.

### Data Management Practices
- **Backups**: Daily automated backups of PostgreSQL.  
- **Row-Level Security (RLS)**: Policies ensure each user sees only their own mixes.  
- **Retention policy**: Old backups purged after a set period (e.g., 30 days).

## 3. Database Schema

Below is a human-readable summary of the main tables. An SQL script follows.

• **sounds**: Stores metadata about each ambient sound.
  – id (UUID)  
  – name (text)  
  – filename (text) – path in storage  
  – tags (text[]) – e.g., ["rain", "forest"]  
  – created_at (timestamp)

• **mixes**: Stores each user’s custom mixes.
  – id (UUID)  
  – user_id (UUID) – references Supabase Auth user  
  – name (text) – user-given mix name  
  – tracks (JSON) – array of objects { sound_id, volume, fade_in, fade_out, crossfade }  
  – created_at (timestamp)  
  – updated_at (timestamp)

SQL (PostgreSQL) schema:
```sql
-- Enable UUID extension
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Table: sounds
CREATE TABLE sounds (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    name TEXT NOT NULL,
    filename TEXT NOT NULL,
    tags TEXT[] DEFAULT ARRAY[]::TEXT[],
    created_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);

-- Table: mixes
CREATE TABLE mixes (
    id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
    user_id UUID NOT NULL,
    name TEXT NOT NULL,
    tracks JSONB NOT NULL,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT now(),
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT now()
);

-- Indexes for performance
CREATE INDEX ON sounds (name);
CREATE INDEX ON mixes (user_id);

-- Row-Level Security for mixes: only owner can access
ALTER TABLE mixes ENABLE ROW LEVEL SECURITY;
CREATE POLICY "Users can manage their mixes" ON mixes
  FOR ALL
  USING (auth.uid() = user_id)
  WITH CHECK (auth.uid() = user_id);
```  

## 4. API Design and Endpoints

We use Supabase’s auto-generated RESTful API to interact with Auth, Storage, and the database.

### Authentication Endpoints (Supabase Auth)
- **Sign up**: POST `/auth/v1/signup`  
- **Sign in**: POST `/auth/v1/token?grant_type=password`  
- **Apple Sign-In**: Handled by Supabase OAuth flow  
- **Sign out**: DELETE `/auth/v1/logout`

### Database Endpoints (RESTful)
- **Fetch sounds**: GET `/rest/v1/sounds`  
  - Supports filtering (e.g., `?tags=cs.{rain}`) and pagination.  
- **Fetch user mixes**: GET `/rest/v1/mixes?user_id=eq.{current_user}`  
- **Create mix**: POST `/rest/v1/mixes` 
  - Body: `{ name, tracks, user_id }`  
- **Update mix**: PATCH `/rest/v1/mixes?id=eq.{mix_id}`  
- **Delete mix**: DELETE `/rest/v1/mixes?id=eq.{mix_id}`

### Storage Endpoints (Audio Files)
- **Download file**: `supabase.storage.from('sounds').download(filename)`  
- **Get public URL**: `supabase.storage.from('sounds').getPublicUrl(filename)`

All endpoints require an `Authorization: Bearer <JWT>` header (except public sound fetch if made public).

## 5. Hosting Solutions

### Supabase Cloud
- **Managed by Supabase**: We use the hosted service at supabase.com.  
- **Regions**: We choose the region closest to our user base for minimal latency (e.g., `us-east-1` or `eu-west-1`).

### Benefits
- **High availability**: Built-in failover and replication  
- **Auto-scaling**: Scales storage and database capacity as needed  
- **Cost-effective**: Pay-as-you-go model, no server management overhead

## 6. Infrastructure Components

Although most infrastructure is handled by Supabase, here’s what’s involved under the hood:

• **API Gateway & Load Balancer**  
  - Routes requests to the appropriate service (Auth, DB, Storage).  
  - Distributes load across multiple instances.

• **PostgreSQL Cluster**  
  - Primary and read replicas  
  - Automatic failover

• **Object Storage**  
  - S3-compatible storage (R2 under the hood)  
  - Integrated CDN for low-latency audio delivery

• **Caching Layer**  
  - CDN caches audio files  
  - Database query caching at the edge

• **DNS & SSL**  
  - Custom domain or supabase.io subdomain  
  - Automatic HTTPS via Let’s Encrypt

## 7. Security Measures

• **Authentication & Authorization**  
  - JWT-based auth via Supabase Auth  
  - Row-Level Security in PostgreSQL to isolate user data

• **Data Encryption**  
  - In transit: HTTPS/TLS for all API calls  
  - At rest: Supabase encrypts database and storage

• **Secure Storage of Credentials**  
  - No plaintext secrets in the app  
  - API keys stored in secure runtime environment or iOS keychain

• **GDPR Compliance**  
  - Users can opt in/out of analytics  
  - Ability to delete all user data on request  

## 8. Monitoring and Maintenance

### Monitoring
- **Supabase Dashboard**: Real-time metrics on database performance, bandwidth, and storage usage.  
- **Logging**: Supabase provides request logs for Auth, Storage, and DB RPCs.

### Alerts & Notifications
- Set up alerts for unusual error rates, high latency, or storage thresholds.

### Maintenance Practices
- **Regular Backups**: Automated daily PostgreSQL backups.  
- **Versioning**: Use Supabase project staging and production instances for safe rollouts.  
- **Policy Reviews**: Quarterly review of RLS policies and indexes for performance.

## 9. Conclusion and Overall Backend Summary

The Relaxing App backend leverages Supabase to deliver a fully managed, scalable, and secure environment:

- **Authentication**: Email/password and Apple Sign-In via Supabase Auth.  
- **Data storage**: PostgreSQL for structured data (sounds catalog, user mixes), with row-level security.  
- **Audio delivery**: Supabase Storage + CDN for fast, reliable streaming and downloads.  
- **Infrastructure**: Fully managed by Supabase—no servers to maintain.  
- **Security & Compliance**: HTTPS everywhere, data encryption, GDPR-ready.

This setup aligns with our goals of fast performance, minimal maintenance overhead, and strong data protection. Any team member, regardless of their background, can now understand how our backend is structured and maintained.