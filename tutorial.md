# Set the theme color with next Themes

1. Add a provider file and wrap it with themeProvider.
2. Add provider file in layout file.

```js
import { Providers } from "./components/Provider";

<Providers>
  <Navbar />
  {children}
</Providers>;
```

3. In ThemeProvider file,

```js
"use client";

import { ThemeProvider } from "next-themes";
import { ReactNode } from "react";

export function Providers({ children }: { children: ReactNode }) {
  return <ThemeProvider attribute="class">{children}</ThemeProvider>;
}
```

4. Create a theme button.
   ThemeButton.jsx

```js
"use client";

import { useTheme } from "next-themes";
import { useEffect, useState } from "react";

export default function Themebutton() {
  const { setTheme, resolvedTheme } = useTheme();

  const [mounted, setMounted] = useState(false);

  useEffect(() => {
    setMounted(true);
  }, []);

  if (!mounted) {
    return null;
  }

  return (
    <button
      onClick={() => setTheme(resolvedTheme === "dark" ? "light" : "dark")}
    >
      {resolvedTheme === "dark" ? (
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          strokeWidth={1.5}
          stroke="currentColor"
          className="w-6 h-6"
        >
          <path
            strokeLinecap="round"
            strokeLinejoin="round"
            d="M12 3v2.25m6.364.386l-1.591 1.591M21 12h-2.25m-.386 6.364l-1.591-1.591M12 18.75V21m-4.773-4.227l-1.591 1.591M5.25 12H3m4.227-4.773L5.636 5.636M15.75 12a3.75 3.75 0 11-7.5 0 3.75 3.75 0 017.5 0z"
          />
        </svg>
      ) : (
        <svg
          xmlns="http://www.w3.org/2000/svg"
          fill="none"
          viewBox="0 0 24 24"
          strokeWidth={1.5}
          stroke="currentColor"
          className="w-6 h-6"
        >
          <path
            strokeLinecap="round"
            strokeLinejoin="round"
            d="M21.752 15.002A9.718 9.718 0 0118 15.75c-5.385 0-9.75-4.365-9.75-9.75 0-1.33.266-2.597.748-3.752A9.753 9.753 0 003 11.25C3 16.635 7.365 21 12.75 21a9.753 9.753 0 009.002-5.998z"
          />
        </svg>
      )}
    </button>
  );
}
```

5. Import ThemeButton in the navbar.
<!-- DONE -->

# Sanity Io

1. npm create sanity@latest -- --template clean --create-project "Sanity Project" --dataset production

2. sanity folder will be created.
3. Add the schema in the schema in new file in schema folder.
4. Go to the sanity folder and start server.
5. For demo create 2 posts.
6. Now we need to get the posts. For that we need to create clients.
7. npm i next-sanity
8. Create file sanity.ts in app/lib/sanity.ts
9. Create a client =>

```js
import { createClient } from "next-sanity";

const projectId = "c1qwa3ql";

const dataset = "production";

const apiVersion = "2023-01-01";

export const client = createClient({
  projectId,
  dataset,
  apiVersion,
  useCdn: true,
});
```

10. Create function for getting the data.

```js
async function getData() {
  // to get all the posts
  const query = `*[_type=='post']`;

  const data = await client.fetch(query);

  return data;
}
```

11.

```js
const data = (await getData()) as Post[];
```

"as post" means we created a file in interface, that tells the type of the values in the data

12. Map the data folder.

Now if we click on the post, postDetails should get opened.

13. Create a link tag on the post with slug.

```js
// put it in map method.
  <Link
    href={`/post/${post.slug.current}`}
    prefetch
    className="space-y-3 xl:col-span-3"
    >
```

14. create a folder =>

app/post/[slug]/page.tsx

15. Get the params in page.tsx.

```js
export default async function SlugPage({
  params,
}: {
  params: { slug: string },
}) {}
```

16. npm i @portabletext/react

```js
<PortableText value={data.content} components={PortableTextComponent} />
```

17. To make images work

- npm i @sanity/image-url

- Create a file in libs

app/libs/sanityImageUrl.ts

```js
import ImageUrlBuilder from "@sanity/image-url";
import { client } from "./sanity";

const builder = ImageUrlBuilder(client);

export function urlFor(source: any) {
  return builder.image(source);
}
```

- Create a function

```js
const PortableTextComponent = {
  types: {
    image: ({ value }: { value: any }) => (
      <Image
        src="{urlFor(value).url()}"
        alt="Image"
        className="rounded-lg"
        width={800}
        height={800}
      />
    ),
  },
};
```
