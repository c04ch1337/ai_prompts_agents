### Can Google AI Studio Handle This?

Yes, **Google AI Studio** (powered by Gemini models) is perfectly capable of generating secure, error-free TypeScript code for your plug-and-play frontend setup. It's a web-based IDE for prototyping with Google's generative AI, and it excels at code generation—including TypeScript APIs, env-based configurations, and Zod schemas for runtime validation. 

Key reasons it fits:
- **Code Generation**: Gemini can output structured TypeScript code directly from prompts, including variables for APIs, .env integration (e.g., via `import.meta.env`), and dynamic endpoints.
- **Secure & Error-Free**: It supports JSON Schema outputs (natively compatible with Zod via adapters like `gemini-zod`), ensuring type-safe, validated responses. You can prompt for best practices like const assertions, readonly types, and error handling to minimize runtime issues.
- **Plug-and-Play**: It can generate env-aware configs (e.g., Vite-style variables) that swap seamlessly between dev/prod, just like we discussed.
- **Limitations**: Outputs are prompt-dependent—be specific for production-ready code. Free tier uses your data for training (switch to paid for privacy). No direct execution/debugging in Studio; export to your IDE for testing.

Recent updates (as of late 2025) include "vibe coding" for rapid app prototyping and full JSON Schema support, making Zod integration a breeze. Tools like Vercel's AI SDK even pair Gemini with Zod out-of-the-box.

### Prompt to Use in Google AI Studio

Copy-paste this **optimized prompt** directly into Google AI Studio's Chat interface (select Gemini 1.5 Pro or Flash for code tasks). It references our conversation's structure for context—Gemini will generate the full codebase. Set "Structured Output" to JSON if you want schema-enforced results, or just hit "Generate" for plain code.

---

**Prompt:**

You are an expert TypeScript developer specializing in secure, production-ready frontend APIs. Generate a complete, plug-and-play TypeScript codebase for a React/Vite app that handles API endpoints, environment variables, type definitions, and Zod schema validation. Make it error-free with full type safety, no hardcoded values, and easy swaps via .env files.

**Requirements:**
- **API Config**: Create `src/config/api.ts` with a base URL from `import.meta.env.VITE_API_BASE_URL` (default to 'http://localhost:3000/api'). Export an `API` object with const-asserted strings for endpoints like:
  - Auth: LOGIN (`/auth/login`), REGISTER (`/auth/register`), REFRESH_TOKEN (`/auth/refresh`), LOGOUT (`/auth/logout`).
  - Users: ME (`/users/me`), USERS (`/users`), USER_BY_ID(id: string|number) as a function.
  - Posts: POSTS (`/posts`), POST_BY_ID(id: string|number), CREATE_POST (`/posts`).
  - Products: PRODUCTS (`/products`).
  Use template literals for full URLs. Add `type ApiEndpoints = typeof API;`.
- **Response Types**: In `src/types/api-responses.ts`, define:
  - `ApiResponse<T>`: { success: boolean; data: T; error?: string; message?: string; meta?: { pagination?: { page, limit, total, totalPages }; } }.
  - `ApiErrorResponse`: { success: false; error: string; code?: string; details?: Record<string, string[]> }.
  - Auth: `LoginResponse` { user: User; tokens: { accessToken, refreshToken, expiresIn } }; `RegisterResponse` { user: User; message: string }; etc.
  - User: `User` { id: string|number; email: string; name: string; avatar?: string|null; role: 'admin'|'user'|'moderator'; isActive: boolean; createdAt/updatedAt: string }.
  - Post: `Post` { id, title, content, excerpt?, slug, status: 'draft'|'published'|'archived', authorId, author?: User, publishedAt?, createdAt, updatedAt, tags?: string[] }.
  - Generic: `PaginatedResponse<T>` for lists with { items: T[]; pagination: { page, limit, total, totalPages } }.
- **Zod Schemas**: In `src/schemas/index.ts`, mirror the types with Zod:
  - `UserSchema = z.object({ id: z.union([z.string(), z.number()]), email: z.string().email(), ... })`.
  - `LoginResponseSchema = BaseApiResponseSchema.extend({ data: z.object({ user: UserSchema, tokens: ... }) })`.
  - Add `PostSchema`, `PaginatedPostsSchema`, and a factory `createPaginatedSchema<T>(itemSchema)`.
  - Export types via `z.infer<typeof Schema>`.
  - Base: `BaseApiResponseSchema = z.object({ success: z.boolean(), ... })`.
- **Safe API Client**: In `src/lib/safeApi.ts`, create an Axios instance with baseURL from env. Add methods like:
  - `login(creds: {email: string; password: string}): Promise<z.infer<typeof LoginResponseSchema>>` – POST to API.LOGIN, parse with schema.parse(res.data).
  - `getMe(): Promise<User>` – GET to API.ME, return parsed user.
  - `getPosts(page=1): Promise<z.infer<typeof PaginatedPostsSchema>>` – GET with params, safeParse for errors.
  Use interceptors for auto-parsing and error throwing.
- **Security & Best Practices**:
  - Use `as const` and `satisfies` for immutability.
  - Handle errors gracefully (e.g., safeParse with console.error on failure).
  - No sensitive data; assume HTTPS in prod.
  - Compatible with Vite/React; include .env examples for dev/prod.
- **Usage Example**: Provide a sample hook/component using the safeApi, e.g., a login form with React Hook Form + zodResolver.

Output as a structured ZIP-ready folder structure with code blocks. Explain briefly how to integrate (e.g., npm i axios zod @hookform/resolvers). Ensure zero runtime errors and full IntelliSense support.

---

**Tips for Using This in AI Studio:**
- **Tune It**: After generation, iterate with follow-ups like "Add auth token interceptor" or "Fix this edge case."
- **Export & Test**: Download as .ts files, paste into your project, run `tsc --noEmit` to verify types.
- **Paid Tier**: For privacy (no data used for training), link a Google Cloud billing account—it's free for light use.

This prompt is dense and self-contained to get high-quality output on the first try. If Gemini hallucinates, refine with "Stick to exact schemas provided." Let me know how it goes—you'll have your unbreakable frontend in minutes! 🚀