
# Supabase + React (JSX) CRUD + RLS Cheat Sheet

## Setup

```jsx
import { createClient } from '@supabase/supabase-js'

const supabaseUrl = 'YOUR_SUPABASE_URL'
const supabaseKey = 'YOUR_SUPABASE_ANON_KEY'
const supabase = createClient(supabaseUrl, supabaseKey)
```

---

## 1. GET — Fetch data

```jsx
async function fetchMessages() {
  const { data, error } = await supabase
    .from('chat')
    .select('*')               // Select all columns
    .order('created_at', { ascending: true }) // Order by timestamp
    .limit(50)                // Limit results

  if (error) {
    console.error('Error fetching messages:', error)
  } else {
    return data
  }
}
```

---

## 2. POST — Insert data

```jsx
async function sendMessage(content) {
  const { data, error } = await supabase
    .from('chat')
    .insert([{ content }])  // Insert new message

  if (error) {
    console.error('Error sending message:', error)
  } else {
    return data
  }
}
```

---

## 3. UPDATE — Update data

```jsx
async function updateMessage(id, newContent) {
  const { data, error } = await supabase
    .from('chat')
    .update({ content: newContent })
    .eq('id', id)           // Filter by id

  if (error) {
    console.error('Error updating message:', error)
  } else {
    return data
  }
}
```

---

## 4. DELETE — Remove data

```jsx
async function deleteMessage(id) {
  const { data, error } = await supabase
    .from('chat')
    .delete()
    .eq('id', id)

  if (error) {
    console.error('Error deleting message:', error)
  } else {
    return data
  }
}
```

---

# Row Level Security (RLS) Basics

Supabase uses PostgreSQL’s RLS policies to control access.

---

## 1. Enable RLS on a table:

```sql
ALTER TABLE chat ENABLE ROW LEVEL SECURITY;
```

---

## 2. Allow authenticated users to SELECT rows

```sql
CREATE POLICY "Allow logged-in users to select chat"
ON chat FOR SELECT
USING (auth.uid() IS NOT NULL);
```

---

## 3. Allow authenticated users to INSERT rows

```sql
CREATE POLICY "Allow logged-in users to insert chat"
ON chat FOR INSERT
WITH CHECK (auth.uid() IS NOT NULL);
```

---

## 4. Allow users to UPDATE and DELETE only their own messages

Assuming `user_id UUID` column linked to `auth.users.id`:

```sql
CREATE POLICY "Allow users to update own messages"
ON chat FOR UPDATE
USING (user_id = auth.uid());

CREATE POLICY "Allow users to delete own messages"
ON chat FOR DELETE
USING (user_id = auth.uid());
```

---

## 5. Insert data with user_id in React

```jsx
const user = supabase.auth.user()
await supabase
  .from('chat')
  .insert([{ content: "Hi", user_id: user.id }])
```

---
## Supabase Auth Basic Example
```jsx
async function signIn(email, password) {
  const { data, error } = await supabase.auth.signInWithPassword({ email, password })
  if (error) {
    console.error('Login error:', error.message)
  } else {
    console.log('User signed in:', data.user)
  }
}
```

Get current user
```jsx
const user = supabase.auth.user()
console.log('Current user:', user)
```

---




