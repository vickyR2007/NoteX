# NoteX — Student Knowledge Hub

A beginner-friendly React + Vite starter for an AI-powered student notes-sharing platform.

## What's working in this first version

- Responsive black-and-violet dashboard
- Search notes by title, subject, author, college, and description
- Filter notes by subject
- Save/unsave notes in the current page session
- Upload a PDF (max 20 MB) and preview/download it in the current browser session
- No demo notes; the library starts empty and displays only actual PDFs uploaded in the current browser session

## Important limitations

This is a frontend prototype. The library starts empty. Uploaded PDFs are held in browser memory and are not shared with other users or saved to a cloud database. Favorites are also temporary. Do not upload sensitive documents.

## Requirements

- Node.js LTS (includes npm)
- VS Code or another code editor
- Internet connection for the first dependency install

## Run on Windows PowerShell

1. Extract the ZIP file.
2. Open the extracted `notex-starter` folder in VS Code.
3. Open Terminal → New Terminal.
4. Check Node.js and npm:

   ```powershell
   node -v
   npm -v
   ```

5. Install dependencies:

   ```powershell
   npm install
   ```

6. Start the development server:

   ```powershell
   npm run dev
   ```

7. Vite will print a local address, usually `http://localhost:5173`. Ctrl+click it or open it in your browser.

If PowerShell says `npm` is not recognized, install the current Node.js LTS release from https://nodejs.org, close and reopen VS Code, then retry `node -v` and `npm -v`.

## Project structure

```text
notex-starter/
├── index.html
├── package.json
├── vite.config.js
├── README.md
└── src/
    ├── main.jsx
    ├── App.jsx
    └── styles.css
```

## Next build stages

1. Add Supabase authentication, database tables, and cloud file storage.
2. Create a Python API for PDF text extraction and chunking.
3. Add embeddings and a vector store for retrieval.
4. Add an LLM endpoint that answers questions using retrieved note passages.
5. Add citations to source pages, moderation, and upload permissions.


## Supabase cloud setup

1. Run `npm install` in this folder.
2. Copy `.env.example` to `.env.local`. Set `VITE_SUPABASE_URL` to your Supabase project URL and `VITE_SUPABASE_PUBLISHABLE_KEY` to the publishable key. Never put a service-role/secret key in frontend code.
3. In Supabase, keep the `notes-pdfs` bucket private. Enable Email authentication.
4. The `public.notes` table must have `title`, `subject`, `description`, `file_path`, `user_id`, and `created_at` columns. Enable RLS.
5. Storage upload policy must restrict inserts to authenticated users' own first-level folder and PDFs. To allow signed-in students to read shared PDFs in the community, add the read policy below (it intentionally grants read access to authenticated users only):

```sql
create policy "Authenticated students can read shared NoteX PDFs"
on storage.objects
for select
to authenticated
using (bucket_id = 'notes-pdfs' and lower(storage.extension(name)) = 'pdf');
```

The app creates short-lived signed URLs after authenticated users request a PDF. For a real public launch, add moderation, rate limits, file scanning, and clear community rules.

Run locally with `npm run dev`; build with `npm run build`. After testing, redeploy with `vercel --prod`.
