---
title: "React Hooks Rehberi: Modern React Geliştirme"
date: "2025-02-01"
author: "Enes Gündüz"
description: "React Hooks ile modern ve etkili React uygulamaları geliştirme rehberi"
tags: ["react", "hooks", "javascript", "frontend", "web-development"]
---

# React Hooks Rehberi: Modern React Geliştirme

React Hooks, React 16.8 ile birlikte tanıtılan ve fonksiyonel bileşenlerde state ve lifecycle özelliklerini kullanmamızı sağlayan güçlü bir özelliktir.

## useState Hook

State yönetimi için en temel hook'tur.

```javascript
import React, { useState } from 'react';

function Counter() {
    const [count, setCount] = useState(0);

    return (
        <div>
            <p>Sayaç: {count}</p>
            <button onClick={() => setCount(count + 1)}>
                Artır
            </button>
            <button onClick={() => setCount(count - 1)}>
                Azalt
            </button>
        </div>
    );
}
```

### Obje State Yönetimi

```javascript
function UserForm() {
    const [user, setUser] = useState({
        name: '',
        email: '',
        age: 0
    });

    const updateUser = (field, value) => {
        setUser(prev => ({
            ...prev,
            [field]: value
        }));
    };

    return (
        <form>
            <input 
                value={user.name}
                onChange={(e) => updateUser('name', e.target.value)}
                placeholder="İsim"
            />
            <input 
                value={user.email}
                onChange={(e) => updateUser('email', e.target.value)}
                placeholder="Email"
            />
        </form>
    );
}
```

## useEffect Hook

Yan etkiler (side effects) için kullanılır.

```javascript
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
    const [user, setUser] = useState(null);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
        const fetchUser = async () => {
            setLoading(true);
            try {
                const response = await fetch(`/api/users/${userId}`);
                const userData = await response.json();
                setUser(userData);
            } catch (error) {
                console.error('Kullanıcı yüklenemedi:', error);
            } finally {
                setLoading(false);
            }
        };

        if (userId) {
            fetchUser();
        }
    }, [userId]); // userId değiştiğinde tekrar çalışır

    if (loading) return <div>Yükleniyor...</div>;
    if (!user) return <div>Kullanıcı bulunamadı</div>;

    return (
        <div>
            <h2>{user.name}</h2>
            <p>{user.email}</p>
        </div>
    );
}
```

### Cleanup Fonksiyonları

```javascript
useEffect(() => {
    const timer = setInterval(() => {
        console.log('Timer çalışıyor');
    }, 1000);

    // Cleanup
    return () => {
        clearInterval(timer);
    };
}, []);
```

## useContext Hook

Context API ile global state yönetimi.

```javascript
import React, { createContext, useContext, useState } from 'react';

// Context oluştur
const ThemeContext = createContext();

// Provider bileşeni
function ThemeProvider({ children }) {
    const [theme, setTheme] = useState('light');

    const toggleTheme = () => {
        setTheme(prev => prev === 'light' ? 'dark' : 'light');
    };

    return (
        <ThemeContext.Provider value={{ theme, toggleTheme }}>
            {children}
        </ThemeContext.Provider>
    );
}

// Hook kullanımı
function ThemedButton() {
    const { theme, toggleTheme } = useContext(ThemeContext);

    return (
        <button 
            onClick={toggleTheme}
            style={{
                backgroundColor: theme === 'light' ? '#fff' : '#333',
                color: theme === 'light' ? '#333' : '#fff'
            }}
        >
            Temayı Değiştir ({theme})
        </button>
    );
}
```

## useReducer Hook

Karmaşık state yönetimi için.

```javascript
import React, { useReducer } from 'react';

// Reducer fonksiyonu
function todoReducer(state, action) {
    switch (action.type) {
        case 'ADD_TODO':
            return [
                ...state,
                {
                    id: Date.now(),
                    text: action.payload,
                    completed: false
                }
            ];
        case 'TOGGLE_TODO':
            return state.map(todo =>
                todo.id === action.payload
                    ? { ...todo, completed: !todo.completed }
                    : todo
            );
        case 'DELETE_TODO':
            return state.filter(todo => todo.id !== action.payload);
        default:
            return state;
    }
}

function TodoApp() {
    const [todos, dispatch] = useReducer(todoReducer, []);
    const [inputValue, setInputValue] = useState('');

    const addTodo = () => {
        if (inputValue.trim()) {
            dispatch({ type: 'ADD_TODO', payload: inputValue });
            setInputValue('');
        }
    };

    return (
        <div>
            <input 
                value={inputValue}
                onChange={(e) => setInputValue(e.target.value)}
                placeholder="Yeni görev"
            />
            <button onClick={addTodo}>Ekle</button>
            
            <ul>
                {todos.map(todo => (
                    <li key={todo.id}>
                        <span 
                            style={{ 
                                textDecoration: todo.completed ? 'line-through' : 'none' 
                            }}
                            onClick={() => dispatch({ type: 'TOGGLE_TODO', payload: todo.id })}
                        >
                            {todo.text}
                        </span>
                        <button onClick={() => dispatch({ type: 'DELETE_TODO', payload: todo.id })}>
                            Sil
                        </button>
                    </li>
                ))}
            </ul>
        </div>
    );
}
```

## Custom Hooks

Kendi hook'larınızı oluşturun:

```javascript
// useFetch custom hook
function useFetch(url) {
    const [data, setData] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
        const fetchData = async () => {
            try {
                setLoading(true);
                const response = await fetch(url);
                const result = await response.json();
                setData(result);
            } catch (err) {
                setError(err);
            } finally {
                setLoading(false);
            }
        };

        fetchData();
    }, [url]);

    return { data, loading, error };
}

// Kullanımı
function UserList() {
    const { data: users, loading, error } = useFetch('/api/users');

    if (loading) return <div>Yükleniyor...</div>;
    if (error) return <div>Hata: {error.message}</div>;

    return (
        <ul>
            {users?.map(user => (
                <li key={user.id}>{user.name}</li>
            ))}
        </ul>
    );
}
```

## Hooks Kuralları

1. **Sadece fonksiyonların en üst seviyesinde kullanın**
2. **Sadece React fonksiyonlarında kullanın**
3. **Koşullu olarak kullanmayın**

```javascript
// ❌ Yanlış
function Component() {
    if (condition) {
        const [state, setState] = useState(0); // Koşullu hook kullanımı
    }
    return <div>...</div>;
}

// ✅ Doğru
function Component() {
    const [state, setState] = useState(0);
    
    if (condition) {
        // Hook'u koşullu olarak kullanma, state'i koşullu olarak kullan
        setState(newValue);
    }
    
    return <div>...</div>;
}
```

## Sonuç

React Hooks ile:

- ✅ Daha temiz ve okunabilir kod
- ✅ State logic'i yeniden kullanabilir
- ✅ Fonksiyonel bileşenlerde tam güç
- ✅ Class bileşenlere gerek yok

React Hooks modern React geliştirmenin olmazsa olmazıdır. Bu rehberle hook'ları etkili bir şekilde kullanabilirsiniz!

---

*Bu yazıda React Hooks'un temellerini öğrendik. Sorularınız için benimle iletişime geçebilirsiniz.*
