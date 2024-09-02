# Vite Config


```javascript
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";
import path from "node:path";
import process from "node:process";
import htmlPurge from 'vite-plugin-purgecss';


// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react(), htmlPurge([htmlPurge()])],
  server: {
    port: 3000,
  },
  resolve: {
    alias: {
      "@": path.resolve(process.cwd(), "src"),
    },
  },
});

```
