---
title: "TypeScript İpuçları: JavaScript'i Güçlendirin"
date: "2025-02-15"
author: "Enes Gündüz"
description: "TypeScript ile daha güvenli ve maintainable kod yazma teknikleri"
tags: ["typescript", "javascript", "programming", "web-development", "types"]
---

# TypeScript İpuçları: JavaScript'i Güçlendirin

TypeScript, JavaScript'e static type checking özelliği ekleyerek daha güvenli ve maintainable kod yazmamızı sağlar. Bu yazıda TypeScript'in gücünü ortaya çıkaracak ipuçlarını paylaşacağım.

## Temel Type Tanımlamaları

### Primitive Types

```typescript
// Temel tipler
let name: string = "Enes";
let age: number = 25;
let isActive: boolean = true;
let data: null = null;
let value: undefined = undefined;

// Array tipler
let numbers: number[] = [1, 2, 3];
let names: Array<string> = ["Ali", "Veli"];

// Tuple
let person: [string, number] = ["Enes", 25];
```

### Object Types

```typescript
// Interface tanımlama
interface User {
    id: number;
    name: string;
    email: string;
    isActive?: boolean; // Optional property
    readonly createdAt: Date; // Readonly property
}

// Type alias
type Product = {
    id: number;
    title: string;
    price: number;
    category: string;
};

// Kullanım
const user: User = {
    id: 1,
    name: "Enes Gündüz",
    email: "enes@example.com",
    createdAt: new Date()
};
```

## Advanced Types

### Union Types

```typescript
type Status = "loading" | "success" | "error";
type ID = string | number;

function handleResponse(status: Status) {
    switch (status) {
        case "loading":
            return "Yükleniyor...";
        case "success":
            return "Başarılı!";
        case "error":
            return "Hata oluştu!";
    }
}
```

### Intersection Types

```typescript
interface Person {
    name: string;
    age: number;
}

interface Employee {
    company: string;
    position: string;
}

// İki interface'i birleştir
type EmployeePerson = Person & Employee;

const employee: EmployeePerson = {
    name: "Enes",
    age: 25,
    company: "TechCorp",
    position: "Developer"
};
```

### Generic Types

```typescript
// Generic function
function identity<T>(arg: T): T {
    return arg;
}

const stringResult = identity<string>("hello"); // string
const numberResult = identity<number>(42); // number

// Generic interface
interface ApiResponse<T> {
    data: T;
    status: number;
    message: string;
}

interface User {
    id: number;
    name: string;
}

const userResponse: ApiResponse<User> = {
    data: { id: 1, name: "Enes" },
    status: 200,
    message: "Success"
};

// Generic constraint
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length); // Artık .length property'sine erişebiliriz
    return arg;
}
```

## Utility Types

TypeScript'in yerleşik utility type'ları:

```typescript
interface User {
    id: number;
    name: string;
    email: string;
    password: string;
}

// Partial - Tüm property'leri optional yapar
type PartialUser = Partial<User>;
// { id?: number; name?: string; email?: string; password?: string; }

// Pick - Belirli property'leri seçer
type UserProfile = Pick<User, "id" | "name" | "email">;
// { id: number; name: string; email: string; }

// Omit - Belirli property'leri hariç tutar
type CreateUser = Omit<User, "id">;
// { name: string; email: string; password: string; }

// Required - Tüm property'leri zorunlu yapar
type RequiredUser = Required<PartialUser>;

// Record - Key-value çiftleri oluşturur
type UserRoles = Record<string, string>;
// { [key: string]: string; }

const roles: UserRoles = {
    "admin": "Administrator",
    "user": "Regular User",
    "moderator": "Moderator"
};
```

## Type Guards

```typescript
// typeof type guard
function processValue(value: string | number) {
    if (typeof value === "string") {
        // Bu blokta value string olarak tanınır
        return value.toUpperCase();
    } else {
        // Bu blokta value number olarak tanınır
        return value.toFixed(2);
    }
}

// instanceof type guard
class Dog {
    bark() {
        console.log("Woof!");
    }
}

class Cat {
    meow() {
        console.log("Meow!");
    }
}

function makeSound(animal: Dog | Cat) {
    if (animal instanceof Dog) {
        animal.bark(); // Dog metodu
    } else {
        animal.meow(); // Cat metodu
    }
}

// Custom type guard
interface Bird {
    fly(): void;
    layEggs(): void;
}

interface Fish {
    swim(): void;
    layEggs(): void;
}

function isBird(pet: Bird | Fish): pet is Bird {
    return (pet as Bird).fly !== undefined;
}

function moveAnimal(pet: Bird | Fish) {
    if (isBird(pet)) {
        pet.fly(); // Bird metodu
    } else {
        pet.swim(); // Fish metodu
    }
}
```

## Enum Kullanımı

```typescript
// Numeric enum
enum Direction {
    Up,    // 0
    Down,  // 1
    Left,  // 2
    Right  // 3
}

// String enum
enum Color {
    Red = "red",
    Green = "green",
    Blue = "blue"
}

// Const enum (daha performanslı)
const enum HttpStatus {
    OK = 200,
    NotFound = 404,
    InternalServerError = 500
}

function handleHttpResponse(status: HttpStatus) {
    switch (status) {
        case HttpStatus.OK:
            return "Başarılı";
        case HttpStatus.NotFound:
            return "Bulunamadı";
        case HttpStatus.InternalServerError:
            return "Sunucu hatası";
    }
}
```

## Conditional Types

```typescript
// Basit conditional type
type NonNullable<T> = T extends null | undefined ? never : T;

// Daha karmaşık örnek
type ApiResponse<T> = T extends string 
    ? { message: T } 
    : T extends number 
    ? { code: T } 
    : { data: T };

type StringResponse = ApiResponse<string>; // { message: string }
type NumberResponse = ApiResponse<number>; // { code: number }
type ObjectResponse = ApiResponse<User>; // { data: User }
```

## Mapped Types

```typescript
// Tüm property'leri readonly yapar
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};

// Tüm property'leri optional yapar
type Partial<T> = {
    [P in keyof T]?: T[P];
};

// Custom mapped type
type Getters<T> = {
    [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K];
};

interface Person {
    name: string;
    age: number;
}

type PersonGetters = Getters<Person>;
// {
//     getName: () => string;
//     getAge: () => number;
// }
```

## Best Practices

### 1. Strict Mode Kullanın

```json
// tsconfig.json
{
    "compilerOptions": {
        "strict": true,
        "noImplicitAny": true,
        "strictNullChecks": true,
        "strictFunctionTypes": true
    }
}
```

### 2. Interface vs Type

```typescript
// Interface - extend edilebilir
interface BaseUser {
    id: number;
    name: string;
}

interface AdminUser extends BaseUser {
    permissions: string[];
}

// Type - union ve intersection için daha iyi
type Theme = "light" | "dark";
type UserWithTheme = BaseUser & { theme: Theme };
```

### 3. Unknown vs Any

```typescript
// ❌ Any kullanmayın
let data: any = getData();
data.foo.bar.baz; // Runtime error olabilir

// ✅ Unknown kullanın
let data: unknown = getData();
if (typeof data === "object" && data !== null && "name" in data) {
    console.log((data as { name: string }).name);
}
```

### 4. Type Assertion Yerine Type Guards

```typescript
// ❌ Type assertion
const user = data as User;

// ✅ Type guard
function isUser(data: unknown): data is User {
    return typeof data === "object" && 
           data !== null && 
           "id" in data && 
           "name" in data;
}

if (isUser(data)) {
    console.log(data.name); // Güvenli
}
```

## Sonuç

TypeScript ile daha güvenli kod yazabilirsiniz:

- ✅ Compile-time hata yakalama
- ✅ Better IDE support
- ✅ Code documentation
- ✅ Refactoring güvenliği
- ✅ Team collaboration

TypeScript öğrenmek başta zor gelebilir, ancak uzun vadede kod kalitesini önemli ölçüde artırır.

## Kaynaklar

- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
- [TypeScript Deep Dive](https://basarat.gitbook.io/typescript/)
- [Type Challenges](https://github.com/type-challenges/type-challenges)

---

*Bu yazıda TypeScript'in güçlü özelliklerini öğrendik. Daha fazla TypeScript içeriği için takipte kalın!*
