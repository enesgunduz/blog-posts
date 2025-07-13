---
title: "Web Geliştirme Temelleri: HTML, CSS ve JavaScript"
date: "2025-01-15"
author: "Enes Gündüz"
description: "Web geliştirmenin temel taşları olan HTML, CSS ve JavaScript'e kapsamlı bir giriş"
tags: ["html", "css", "javascript", "web-development", "frontend"]
---

# Web Geliştirme Temelleri: HTML, CSS ve JavaScript

Web geliştirme dünyasına adım atanlar için temel teknolojileri öğrenmek kritik önem taşır. Bu yazıda HTML, CSS ve JavaScript'in temellerini ele alacağız.

## HTML: Web'in İskeleti

HTML (HyperText Markup Language), web sayfalarının yapısını oluşturan işaretleme dilidir.

### Temel HTML Yapısı

```html
<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sayfa Başlığı</title>
</head>
<body>
    <h1>Ana Başlık</h1>
    <p>Bu bir paragraftır.</p>
</body>
</html>
```

### Önemli HTML Etiketleri

- `<header>`: Sayfa başlığı
- `<nav>`: Navigasyon menüsü
- `<main>`: Ana içerik
- `<article>`: Makale içeriği
- `<section>`: Bölümler
- `<footer>`: Sayfa altbilgisi

## CSS: Web'in Stili

CSS (Cascading Style Sheets) ile HTML elementlerine stil verebiliriz.

### Temel CSS Syntaxı

```css
selector {
    property: value;
}

/* Örnek */
h1 {
    color: blue;
    font-size: 24px;
    text-align: center;
}
```

### CSS Flexbox

Modern layout için Flexbox kullanımı:

```css
.container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
}
```

## JavaScript: Web'in Davranışları

JavaScript ile web sayfalarına interaktiflik katarız.

### Temel JavaScript

```javascript
// Değişkenler
let name = "Enes";
const age = 25;

// Fonksiyonlar
function greet(name) {
    return `Merhaba ${name}!`;
}

// DOM Manipulation
document.getElementById('myButton').addEventListener('click', function() {
    alert('Butona tıklandı!');
});
```

### Modern JavaScript (ES6+)

```javascript
// Arrow Functions
const multiply = (a, b) => a * b;

// Template Literals
const message = `Sonuç: ${multiply(5, 3)}`;

// Destructuring
const { name, age } = user;
const [first, second] = numbers;
```

## Sonuç

Bu üç teknoloji web geliştirmenin temelini oluşturur:

1. **HTML** - Yapı
2. **CSS** - Stil
3. **JavaScript** - Davranış

Bu temelleri öğrendikten sonra React, Vue, Angular gibi framework'lere geçiş yapabilirsiniz.

## Kaynaklar

- [MDN Web Docs](https://developer.mozilla.org/)
- [W3Schools](https://www.w3schools.com/)
- [CSS-Tricks](https://css-tricks.com/)

---

*Bu yazı web geliştirme yolculuğunuzun başlangıcı olsun! Sorularınız için benimle iletişime geçebilirsiniz.*
