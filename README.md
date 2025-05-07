# react-unfolder

A react framework-to-be. Name is a placeholder.

## Why?

An attempt to design and build a developer-experience-focused React framework.

The API should be easy enough to fit in a single presentation slide.

## Design (WIP)

The idea is to avoid using folder structure and get more benefit of the TypeScript types avoiding code-generation and ide-extensions where possible, and get a more predictable representaion of the results.

Each route consists of a component, a path matchers, and function that return the props of the component baseed on the matcher.

Link paths and parameters can be inferred from the matchers.

```tsx
import React from "react";
import Unfold, { M } from "react-unfolder";
import Layout from "./components/Layout";

const Home = React.lazy(() => import("./components/Home"));

const HomeRoute = Unfold()(Home);

interface AboutProps {
  section: React.ReactNode;
}

const About = React.lazy(() => import("./components/About"));

const AboutRoute = Unfold(
  // Typed: { section: "" | "first" | "second" }
  M("section").string("", "first", "second")
)(About, ({ section }) => {
  switch (section) {
    case "":
    case "first":
      return {
        section: <FirstAboutSection />,
      };
    case "second":
      return {
        section: <SecondAboutSection />,
      };
  }
});

interface LayoutProps {
  title: string;
  content: React.ReactNode;
}

const App = Unfold(
  // Typed:
  // {
  //   page:
  //     | { name: "" | "home" }
  //     | { name: "about"; section: "" | "first" | "second" };
  // }
  M("page")
    .case(M("name").string("", "home"), HomeRoute)
    .case(M("name").string("about"), AboutRoute)
)(Layout, ({ page: { name } }) => {
  switch (name) {
    case "":
    case "home":
      return {
        title: "Home",
        content: <HomeRoute />,
      };
    case "about":
      return {
        title: "About",
        content: <AboutRoute />,
      };
  }
});

// There are more matchers such as: M('rest').rest() to match the remaining
// segments:
// M("app")("folders", M('folderPath').rest())
// With /folders/some/path it matches:
// { app: { folderPath: 'some/path' } }
// Other matchers may be needed for regex, hash, and query.

export default App;
```
