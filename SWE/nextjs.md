# Next.js

* Traditionaly react apps are basically an empty html and a javascript file containing all the react code and is rendered client side after running the javascript. The downside to this is that the content is not indexed properly by all the search engines and internet bots and the slow first contentful paint because of this approach.
* Next.js on the other hand offers server side rendering which eliminates all the above mentioned problems therefore improving SEO and website performance.
* After the content is recieved by the client, client side rendering takes place.
* It offers the following features:

  * Static Site Generation
  * Server Side Rendering - generate each page at request time.

    * **Good**: where data constantly changes.
    * **Bad**: slower and ineffecient data caching

  * Incremental Static Regeneration: regenerate single pages in background

### Routing Files

* **page** - is a special Next.js file that exports a React component, and it's required for the route to be accessible.
* **layout** - shared UI such as headers,nav or footer
* **loading** - is a special Next.js file built on top of React Suspense. It allows you to create fallback UI to show as a replacement while page content loads.
* **error** - error UI
* **not-found** - Not found UI
* **route** - for API endpoints
* **default** - parallel route fallback page
* **template** - re-rendered layout
* Next.js automatically code splits your application by route segments. This is different from a traditional React SPA, where the browser loads all your application code on the initial page load.

### Nested Routes

* Folders define URL segments. Nesting folders nests segments. Layouts at any level wrap their child segments. A route becomes public when a page or route file exists.

| Path | URL pattern | Notes |
|------|------|------|
| app/layout.tsx | - | Root layout wraps all routes |
| app/blog/layout.tsx | - | Wraps /blog and descendants |
| app/page.tsx | / | Public route |
| app/blog/page.tsx | /blog | Public route |
| app/blog/authors/page.tsx | /blog/authors | Public route |



### Dynamic Routes

Parameterize segments with square brackets.

| Notes | Path | URL |

|-----|-----|-----|

| \[segment] for a single param | app/blog/\[slug]/page.tsx | /blog/my-first-post |

| \[...segment] for catch‑all | app/shop/\[...slug]/page.tsx | /shop/clothing, /shop/clothing/shirts |

| \[\[...segment]] for optional catch‑all | app/docs/\[\[...slug]]/page.tsx | /docs, /docs/layouts-and-pages, /docs/api-reference/use-router |

Access values via the params prop.

### Route groups and private folders

Route groups allow you to organize files into logical groups without affecting the URL path structure.

| Notes | Path | URL |

|-----|-----|-----|

| app/(marketing)/page.tsx | / | Group omitted from URL |

| app/(shop)/cart/page.tsx | /cart | Share layouts within (shop) |

| app/blog/\_components/Post.tsx | - | Not routable; safe place for UI utilities |

| app/blog/\_lib/data.ts | - | Not routable; safe place for utils |

### Streaming

Streaming is a data transfer technique that allows you to break down a route into smaller "chunks" and progressively stream them from the server to the client as they become ready. This can prevent slow data requests from blocking your whole page. This allows the user to see and interact with parts of the page without waiting for all the data to load before any UI can be shown to the user.

