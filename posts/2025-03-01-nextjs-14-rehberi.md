---
title: "Next.js 14 ile Modern Web Uygulamaları"
date: "2025-03-01"
author: "Enes Gündüz"
description: "Next.js 14'ün yeni özellikleri ve modern web uygulaması geliştirme teknikleri"
tags: ["nextjs", "react", "ssr", "app-router", "web-development"]
---

# Next.js 14 ile Modern Web Uygulamaları

Next.js 14, React ekosisteminde tam yığın web uygulamaları geliştirmek için en popüler framework'lerden biridir. Bu yazıda Next.js 14'ün özelliklerini ve modern web uygulaması geliştirme tekniklerini inceleyeceğiz.

## Next.js 14 Yenilikleri

### App Router (Stable)

Next.js 13 ile tanıtılan App Router artık stabil durumda:

```bash
my-app/
├── app/
│   ├── layout.tsx      # Root layout
│   ├── page.tsx        # Home page
│   ├── loading.tsx     # Loading UI
│   ├── error.tsx       # Error UI
│   ├── not-found.tsx   # 404 page
│   └── blog/
│       ├── page.tsx    # /blog
│       └── [slug]/
│           └── page.tsx # /blog/[slug]
```

### Server Components (Default)

```tsx
// app/page.tsx - Server Component (default)
export default async function HomePage() {
    // Bu kod sunucuda çalışır
    const posts = await fetch('https://api.example.com/posts')
        .then(res => res.json());

    return (
        <div>
            <h1>Blog Yazıları</h1>
            {posts.map(post => (
                <article key={post.id}>
                    <h2>{post.title}</h2>
                    <p>{post.excerpt}</p>
                </article>
            ))}
        </div>
    );
}
```

### Client Components

```tsx
'use client'; // Client Component olduğunu belirtir

import { useState } from 'react';

export default function InteractiveButton() {
    const [count, setCount] = useState(0);

    return (
        <button onClick={() => setCount(count + 1)}>
            Clicked {count} times
        </button>
    );
}
```

## Routing ve Navigation

### App Router ile Routing

```tsx
// app/layout.tsx - Root Layout
export default function RootLayout({
    children,
}: {
    children: React.ReactNode;
}) {
    return (
        <html lang="tr">
            <body>
                <nav>
                    <a href="/">Ana Sayfa</a>
                    <a href="/blog">Blog</a>
                    <a href="/about">Hakkımızda</a>
                </nav>
                <main>{children}</main>
            </body>
        </html>
    );
}

// app/blog/page.tsx
export default function BlogPage() {
    return <h1>Blog Sayfası</h1>;
}

// app/blog/[slug]/page.tsx - Dynamic Route
export default function BlogPost({ params }: { params: { slug: string } }) {
    return <h1>Blog Post: {params.slug}</h1>;
}
```

### Navigation

```tsx
'use client';

import Link from 'next/link';
import { useRouter, usePathname } from 'next/navigation';

export default function Navigation() {
    const router = useRouter();
    const pathname = usePathname();

    return (
        <nav>
            <Link 
                href="/" 
                className={pathname === '/' ? 'active' : ''}
            >
                Ana Sayfa
            </Link>
            <Link 
                href="/blog"
                className={pathname === '/blog' ? 'active' : ''}
            >
                Blog
            </Link>
            <button onClick={() => router.push('/contact')}>
                İletişim
            </button>
        </nav>
    );
}
```

## Data Fetching

### Server Components ile Data Fetching

```tsx
// app/posts/page.tsx
async function getPosts() {
    const res = await fetch('https://jsonplaceholder.typicode.com/posts', {
        next: { revalidate: 60 } // 60 saniyede bir yeniden fetch et
    });
    
    if (!res.ok) {
        throw new Error('Failed to fetch posts');
    }
    
    return res.json();
}

export default async function PostsPage() {
    const posts = await getPosts();

    return (
        <div>
            <h1>Blog Posts</h1>
            {posts.map(post => (
                <article key={post.id}>
                    <h2>{post.title}</h2>
                    <p>{post.body}</p>
                </article>
            ))}
        </div>
    );
}
```

### Client-side Data Fetching

```tsx
'use client';

import { useState, useEffect } from 'react';

interface Post {
    id: number;
    title: string;
    body: string;
}

export default function ClientPosts() {
    const [posts, setPosts] = useState<Post[]>([]);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
        fetch('/api/posts')
            .then(res => res.json())
            .then(data => {
                setPosts(data);
                setLoading(false);
            });
    }, []);

    if (loading) return <div>Yükleniyor...</div>;

    return (
        <div>
            {posts.map(post => (
                <article key={post.id}>
                    <h2>{post.title}</h2>
                    <p>{post.body}</p>
                </article>
            ))}
        </div>
    );
}
```

## API Routes

### Route Handlers

```tsx
// app/api/posts/route.ts
import { NextRequest, NextResponse } from 'next/server';

export async function GET(request: NextRequest) {
    try {
        const posts = await fetch('https://jsonplaceholder.typicode.com/posts')
            .then(res => res.json());
        
        return NextResponse.json(posts);
    } catch (error) {
        return NextResponse.json(
            { error: 'Failed to fetch posts' },
            { status: 500 }
        );
    }
}

export async function POST(request: NextRequest) {
    try {
        const body = await request.json();
        
        // Yeni post oluştur
        const newPost = {
            id: Date.now(),
            title: body.title,
            content: body.content,
            createdAt: new Date().toISOString()
        };
        
        return NextResponse.json(newPost, { status: 201 });
    } catch (error) {
        return NextResponse.json(
            { error: 'Failed to create post' },
            { status: 500 }
        );
    }
}
```

### Dynamic API Routes

```tsx
// app/api/posts/[id]/route.ts
import { NextRequest, NextResponse } from 'next/server';

export async function GET(
    request: NextRequest,
    { params }: { params: { id: string } }
) {
    const id = params.id;
    
    try {
        const post = await fetch(`https://jsonplaceholder.typicode.com/posts/${id}`)
            .then(res => res.json());
        
        return NextResponse.json(post);
    } catch (error) {
        return NextResponse.json(
            { error: 'Post not found' },
            { status: 404 }
        );
    }
}
```

## Styling

### CSS Modules

```css
/* components/Button.module.css */
.button {
    background-color: #0070f3;
    color: white;
    border: none;
    padding: 0.5rem 1rem;
    border-radius: 0.25rem;
    cursor: pointer;
}

.button:hover {
    background-color: #0051a2;
}

.primary {
    background-color: #0070f3;
}

.secondary {
    background-color: #666;
}
```

```tsx
// components/Button.tsx
import styles from './Button.module.css';

interface ButtonProps {
    children: React.ReactNode;
    variant?: 'primary' | 'secondary';
    onClick?: () => void;
}

export default function Button({ children, variant = 'primary', onClick }: ButtonProps) {
    return (
        <button 
            className={`${styles.button} ${styles[variant]}`}
            onClick={onClick}
        >
            {children}
        </button>
    );
}
```

### Tailwind CSS

```bash
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
```

```tsx
// app/page.tsx
export default function HomePage() {
    return (
        <div className="min-h-screen bg-gray-100">
            <div className="container mx-auto px-4 py-8">
                <h1 className="text-4xl font-bold text-gray-800 mb-8">
                    Hoş Geldiniz
                </h1>
                <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                    <div className="bg-white rounded-lg shadow-md p-6">
                        <h2 className="text-xl font-semibold mb-4">Card 1</h2>
                        <p className="text-gray-600">Card içeriği...</p>
                    </div>
                </div>
            </div>
        </div>
    );
}
```

## Performance Optimization

### Image Optimization

```tsx
import Image from 'next/image';

export default function ProfilePage() {
    return (
        <div>
            <Image
                src="/profile.jpg"
                alt="Profile Picture"
                width={500}
                height={300}
                priority // LCP için önemli resimler
                placeholder="blur"
                blurDataURL="data:image/jpeg;base64,..." // Blur placeholder
            />
        </div>
    );
}
```

### Font Optimization

```tsx
// app/layout.tsx
import { Inter, Roboto_Mono } from 'next/font/google';

const inter = Inter({
    subsets: ['latin'],
    display: 'swap',
});

const robotoMono = Roboto_Mono({
    subsets: ['latin'],
    display: 'swap',
});

export default function RootLayout({
    children,
}: {
    children: React.ReactNode;
}) {
    return (
        <html lang="tr" className={inter.className}>
            <body>{children}</body>
        </html>
    );
}
```

### Loading and Error Handling

```tsx
// app/blog/loading.tsx
export default function Loading() {
    return (
        <div className="animate-pulse">
            <div className="h-4 bg-gray-300 rounded w-3/4 mb-4"></div>
            <div className="h-4 bg-gray-300 rounded w-1/2 mb-4"></div>
            <div className="h-4 bg-gray-300 rounded w-5/6"></div>
        </div>
    );
}

// app/blog/error.tsx
'use client';

export default function Error({
    error,
    reset,
}: {
    error: Error;
    reset: () => void;
}) {
    return (
        <div className="text-center py-8">
            <h2 className="text-2xl font-bold text-red-600 mb-4">
                Bir Hata Oluştu!
            </h2>
            <p className="text-gray-600 mb-4">{error.message}</p>
            <button 
                onClick={reset}
                className="bg-blue-500 text-white px-4 py-2 rounded"
            >
                Tekrar Dene
            </button>
        </div>
    );
}
```

## Deployment

### Vercel Deployment

```bash
# Vercel CLI ile deploy
npm i -g vercel
vercel

# veya GitHub integration ile otomatik deploy
```

### Environment Variables

```bash
# .env.local
DATABASE_URL=postgresql://localhost:5432/mydb
NEXT_PUBLIC_API_URL=https://api.example.com
SECRET_KEY=your-secret-key
```

```tsx
// Kullanım
const apiUrl = process.env.NEXT_PUBLIC_API_URL; // Client-side
const secretKey = process.env.SECRET_KEY; // Server-side only
```

## Best Practices

### 1. Server vs Client Components

```tsx
// ✅ Server Component kullanın (default)
export default async function ServerComponent() {
    const data = await fetchData();
    return <div>{data}</div>;
}

// ✅ Interaktiflik gerektiğinde Client Component
'use client';
export default function ClientComponent() {
    const [state, setState] = useState(0);
    return <button onClick={() => setState(s => s + 1)}>{state}</button>;
}
```

### 2. Data Fetching Strategy

```tsx
// ✅ Server Component'te data fetch
export default async function Page() {
    const posts = await getPosts(); // Server-side
    return <PostList posts={posts} />;
}

// ✅ Client Component sadece UI state için
'use client';
export default function PostList({ posts }) {
    const [filter, setFilter] = useState('');
    const filteredPosts = posts.filter(post => 
        post.title.includes(filter)
    );
    
    return (
        <div>
            <input 
                value={filter}
                onChange={(e) => setFilter(e.target.value)}
                placeholder="Posts ara..."
            />
            {filteredPosts.map(post => (
                <Article key={post.id} post={post} />
            ))}
        </div>
    );
}
```

## Sonuç

Next.js 14 ile modern web uygulamaları geliştirmek için:

- ✅ App Router kullanın
- ✅ Server Components'i varsayılan olarak tercih edin
- ✅ Client Components'i sadece gerektiğinde kullanın
- ✅ Built-in optimizasyonlardan yararlanın
- ✅ Type safety için TypeScript kullanın

Next.js 14, React ekosisteminde production-ready uygulamalar geliştirmek için mükemmel bir seçimdir!

---

*Bu yazıda Next.js 14'ün temel özelliklerini öğrendik. Daha fazla Next.js içeriği için takipte kalın!*
