# react-unfolder

A react framework-to-be. Name is a placeholder.

## Why?

An attempt to design and build a developer-experience-focused React framework.

The API should be easy enough to fit in a single presentation slide.

## Design (WIP)

The idea is to avoid using folder structure and get more benefit of the TypeScript types avoiding code-generation and ide-extensions where possible, and get a more predictable representaion of the results.

```tsx
interface Context {
  path: string;
  query: Record<string, string | string[]>; // Can be validated for more precise type.
  parameters: {}; // starts empty, adds more props as the path builds.
  body: null; // `null` on client. Potentially typed on the server.
}

const { go, link, App, paths } = Unfolder(
  "/",
  async (
    { title, content }: { title: string; content: React.ReactNode },
    _context: Context
  ) => ({
    app: <Layout {...{ title, content }} />,
  })
)
  .current({ title: "Home", content: <Home /> })
  .to("about", async ({}: {}) => ({
    title: "About",
    content: <About />,
  }))
  .to(
    ["page-with-tabs/", Path.String("activeTab", ["", "tab-a", "tab-b"])],
    async ({}: {}, { parameters: { activeTab } }) => ({
      // activeTab is automatically typed to "" | "tab-a" | "tab-b"
      title: `Page with tabs | ${activeTab}`,
      content: <PageWithTabs activeTab={activeTab} />,
    })
  )
  .to("posts", async ({ selectedPost }: { selectedPost: React.ReactNode }) =>
    Unfolder({
      title: "Posts",
      content: <Posts selectedPost={selectedPost} />,
    })
      .current({ selectedPost: undefined })
      .to(
        [Path.String("postId")],
        async ({}: {}, { parameters: { postId } }) => ({
          selectedPost: <Post id={postId} />,
        })
      )
  );

// `path` is automatically typed to `"/" | "/about"`.
<a href={link(path)} onClick={() => go(path)}>
  Link works with or without JS
</a>;
```
