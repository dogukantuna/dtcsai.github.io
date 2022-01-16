---
title: Sub API w/ Next.js Stack for TypeScript Configuration
date: 2020-06-26
permalink: /posts/2020/06/sub-api-with-next-js-for-typescript/
tags:
  - javascript
---

The main purpose of this post is to show how all these technologies can be combined together to support this with a useful example. You can build whatever you want on this stack that we will build.

# Table of Contents
- Stack Introduction
- Creating a Next.js Custom Express Server with TypeScript Configuration
- Mailchimp API Integration and Creating Submit Form

# 1. Stack Introduction

## Why Next.js?

### 1. Relatively easy to learn.

Next.js offers you advanced tools and a robust API support, but it doesn’t force you to use them.

### 2. Built-in CSS support.

Writing CSS in component-driven frameworks comes with a sacrosanct need for the “cascade”. It’s why you have [CSS-in-JS](https://cssinjs.org/) tools, but Next.js comes out of the box with its own offering — [styled-jsx](https://github.com/vercel/styled-jsx), and also supports a bevy of styling methodologies.

### 3. Automatic TypeScript support.

If you like to code in TypeScript, with Next.js, you have the advantage of TypeScript being compiled and supported automatically for you.

### 4. Multiple data fetching technique.

It supports SSG and SSR. You can choose to use one or the other, or both.

### 4. Multiple data fetching technique.

It supports SSG and SSR. You can choose to use one or the other, or both.

### 5. File-system routing.

Navigating between one page to another is supported through the file-system of your app. You do not need any special library to handle routing.

[Getting Started with Next.js](https://nextjs.org/docs/getting-started)

## Why TypeScript?

[Why You Should Choose TypeScript Over JavaScript](https://serokell.io/blog/why-typescript)

## Why Custom Express Server?

[Advanced Features: Custom Server](https://nextjs.org/docs/advanced-features/custom-server)

# 2. Creating our project

*So, let’s get started!*
First of all, create an empty folder where you will install your project. Then go to the project directory and install all the following dependencies below.

```
npm init
npm install react react-dom next
npm install express
npm install --save-dev typescript @types/node @types/react
npm install --save-dev ts-node @types/express
npm fund
```

## Project Structure

After dependency installation, create the following project structure in your directory. Later, we will update this structure according to our needs.

```
├─ server
│  └─ server.ts
├─ src
│  └─ pages
│     └─ home.tsx
├─ package.json
├─ tsconfig.json
└─ tsconfig.server.json
```

Put the following configuration files and entry point in the files you created.

### // server.ts

```typescript
import express, { Request, Response } from "express";
import next from "next";
const dev = process.env.NODE_ENV !== 'production';
const app = next({ dev });
const handle = app.getRequestHandler();
const port = process.env.PORT || 8080;
(async () => {
    try {
       await app.prepare();
       const server = express();
       server.all("*", (req: Request, res: Response) => {
         return handle(req, res);
       });
       server.listen(port, (err?: any) => { 
         if (err) throw err;  
             console.log(`> Launched on http://localhost:${port}`);});
} catch (e) {
  console.error(e);
  process.exit(1);
}
})();
```

### // index.ts

Now, create an index.tsx that is located in the src/pages directory. And put the following codes in index.tsx file.

```typescript
function HomePage() {
  return <div> <h1>Hello World!</h1> </div>;
}
export default HomePage;
```

### // package.json

Install the scripts below by editing the package.json file.

```json
"scripts": {
  "dev": "ts-node --project tsconfig.server.json server/server.ts",
  "build:server": "tsc --project tsconfig.server.json",
  "build:next": "next build",
  "build": "npm run build:next && npm run build:server",
  "start": "NODE_ENV=production node dist/index.js"
},
```

## Configuration Files

### // tsconfig.json

```json
{
  "compilerOptions": {
     "target": "es6",
     "lib": [
       "dom","dom.iterable", "esnext"],
     "allowJs": true,
     "skipLibCheck": true,
     "strict": false,
     "forceConsistentCasingInFileNames": true,
     "noEmit": true,
     "esModuleInterop": true,
     "module": "esnext",
     "moduleResolution": "node",
     "resolveJsonModule": true,
     "isolatedModules": true,
     "jsx": "preserve" },
     "exclude": [ "node_modules"],
     "include": [ "next-env.d.ts", "**/*.ts", "**/*.tsx"]}
```

### // tsconfig.server.json

```json
{  
   "extends": "./tsconfig.json",  
   "compilerOptions": {    
   "module": "commonjs",
   "outDir": "dist",
   "noEmit": false  },
   "include": ["server"]
}
```

We are now ready to run Next.js on a TypeScript supported custom Express server. If you want to do something else, not the subscription form, you can use the structure we used so far.

# 3. Mailchimp Integration and Creating Submit Form

Before running our project, follow the steps below to integrate Mailchimp API.

### // Updating structure

```
├───server
└───src
    └───pages
        └───api
              └───subscribe.ts
```

### Adding src/pages/api/subscribe.ts

```typescript
import axios from "axios"
function Req(email) {
// Creating API Key: mailchimp.com/developer/marketing/guides/quick-start/
const API_KEY = "KEY HERE";
//Find your ID: mailchimp.com/help/find-audience-id/
const AUDIENCE_ID = "ID HERE"
const DATACENTER = "YOUR DATACENTER TAG";
const url = `https://${DATACENTER}.api.mailchimp.com/3.0/lists/${AUDIENCE_ID}/members`
const data = {
  email_address: email,
  status: "subscribed",
}
const base64ApiKey = Buffer.from(`anystring:${API_KEY}`).toString("base64")
const headers = {
   "Content-Type": "application/json",
   Authorization: `Basic ${base64ApiKey}`,
}
return {
  url,
  data,
  headers,
  }
}
export default async (req, res) => {
   const { email } = req.body
   if (!email || !email.length) {
     return res.status(400).json({
       error: "Forgot to submit your email address?",
   })}
try {
   const { url, data, headers } = Req(email)
   const response = await axios.post(url, data, { headers })
   return res.status(201).json({ error: null })
} catch (error) {
  return res.status(400).json({
     error: `Please enter a valid email address.`,
  })
 }
}
```

### // Updating our src/pages/index.tsx

```typescript
import { useState } from 'react'
import axios from "axios"
export default function Home() {
   const [errorMessage, setErrorMessage] = useState(null)
   const [email, setEmail] = useState("")
   const [state, setState] = useState("IDLE")
const subscribe = async () => {
   setState("LOADING")
   setErrorMessage(null)
   try {
      const response = await axios.post("api/subscribe", { email })
      setState("SUCCESS")
   } catch (e) {
   setErrorMessage(e.response.data.error)
   setState("ERROR")}}
return (
  <div>
   <main>
     <input
      type="email"
      placeholder="Enter your email to subscribe"
      value={email}
      onChange={(e) => setEmail(e.target.value)}
    />
    {state === "ERROR" && <p>{errorMessage}</p>}
    <button
      type="button"
      disabled={state === "LOADING"}
      onClick={subscribe}
    >
    Subscribe
    </button>
    {state === "SUCCESS" && <p><i>You have successfully registered on mailing list! 🎉</i></p>}
   </main>
 </div>
 )
}
```

## Testing

After our complete customization, run the npm run dev command and visit http://localhost:8080 to view our homepage.

# Additional: Styling

If you want to add a style to the sample application we made, create a styles folder in the main directory of your project structure and add your CSS file. Then, import the CSS file you prepared into our index.tsx file and assign a class to the elements you want to style as follows.

```js
// Import
import styles from '../../styles/Home.module.css'
// Example
<input
    className={styles.inputSub}
    type="email"
    placeholder="Enter your email to subscribe"
    value={email}
    onChange={(e) => setEmail(e.target.value)}
/>
```
