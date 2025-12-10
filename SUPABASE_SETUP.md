# Supabase Setup Instructions

Your forum system is now configured to use Supabase for global data synchronization across all users and devices!

## Step 1: Create Supabase Account

1. Go to https://supabase.com
2. Sign up for a free account
3. Click "New Project"
4. Fill in:
   - **Project name**: `TFB-Illegal-CIv-Forum` (or any name you prefer)
   - **Database password**: Choose a strong password (save it!)
   - **Region**: Choose closest to your users
5. Click "Create new project" and wait 1-2 minutes for setup

## Step 2: Create Database Tables

Once your project is ready:

1. Go to the **SQL Editor** tab in the left sidebar
2. Click "New Query"
3. Copy and paste this SQL code:

```sql
-- Create users table
CREATE TABLE users (
  id BIGSERIAL PRIMARY KEY,
  username TEXT UNIQUE NOT NULL,
  password TEXT NOT NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Create threads table
CREATE TABLE threads (
  id TEXT PRIMARY KEY,
  title TEXT NOT NULL,
  scheme_type TEXT NOT NULL,
  thread_type TEXT NOT NULL,
  author TEXT NOT NULL,
  video_url TEXT,
  video_type TEXT,
  comments JSONB DEFAULT '[]'::jsonb,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Enable Row Level Security (RLS)
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE threads ENABLE ROW LEVEL SECURITY;

-- Create policies to allow public access (since you're using client-side auth)
CREATE POLICY "Allow public read access on users" ON users FOR SELECT USING (true);
CREATE POLICY "Allow public insert on users" ON users FOR INSERT WITH CHECK (true);

CREATE POLICY "Allow public read access on threads" ON threads FOR SELECT USING (true);
CREATE POLICY "Allow public insert on threads" ON threads FOR INSERT WITH CHECK (true);
CREATE POLICY "Allow public update on threads" ON threads FOR UPDATE USING (true);
CREATE POLICY "Allow public delete on threads" ON threads FOR DELETE USING (true);

-- Create indexes for better performance
CREATE INDEX idx_threads_created_at ON threads(created_at DESC);
CREATE INDEX idx_threads_author ON threads(author);
CREATE INDEX idx_users_username ON users(username);
```

4. Click **Run** (or press Ctrl/Cmd + Enter)
5. You should see "Success. No rows returned"

## Step 3: Get Your API Credentials

1. Go to **Settings** (⚙️ icon in sidebar)
2. Click **API** in the settings menu
3. Find these two values:

   **Project URL**: `https://xxxxxxxxxxxxx.supabase.co`
   
   **anon/public key**: `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.ey...` (very long string)

## Step 4: Update Your Website

1. Open `Index.html`
2. Find these lines near the top of the `<script>` section (around line 710):

```javascript
const SUPABASE_URL = 'YOUR_SUPABASE_URL_HERE';
const SUPABASE_ANON_KEY = 'YOUR_SUPABASE_ANON_KEY_HERE';
```

3. Replace them with your actual credentials:

```javascript
const SUPABASE_URL = 'https://xxxxxxxxxxxxx.supabase.co';
const SUPABASE_ANON_KEY = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.ey...';
```

4. Save the file
5. Commit and push to GitHub:

```bash
git add Index.html
git commit -m "Add Supabase integration"
git push
```

## Step 5: Test It!

1. Visit your site: https://routeq.github.io/TFB-Illegal-CIv/
2. Open the browser console (F12) - you should see: `✅ Supabase connected - Forum data will be synced globally`
3. Register a new account and create a thread
4. Open the site in a different browser or device
5. Login with a different account - you should see the thread from step 3!

## How It Works

- **Before Supabase**: All data stored in localStorage (device-specific)
- **After Supabase**: All threads, comments, and users stored in cloud database
- **Fallback**: If Supabase has issues, automatically falls back to localStorage

## Troubleshooting

### Console shows "Supabase not configured"
- Make sure you replaced BOTH the URL and API key in Index.html
- Check for typos in the credentials

### Console shows "Supabase initialization failed"
- Verify your Project URL is correct (should start with `https://`)
- Make sure you copied the **anon/public** key, not the service key

### "Failed to create thread" error
- Check the SQL tables were created successfully
- Verify RLS policies are enabled in Supabase dashboard

### Still using localStorage?
- Clear your browser cache and reload
- Check browser console for error messages
- Verify Supabase project is active (not paused)

## Need Help?

Check the Supabase documentation: https://supabase.com/docs

Your forum system is ready for global deployment! 🚀
