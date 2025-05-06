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

const HomeRoute = Unfold(Home);

interface AboutProps {
  section: React.ReactNode;
}

const About = React.lazy(() => import("./components/About"));

const AboutRoute = Unfold(About)(
  M("about", M.String("section", ["", "first", "second"])),
  ({ params }) => {
    switch (params.section) {
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
  }
);

interface LayoutProps {
  title: string;
  content: React.ReactNode;
}

const App = Unfold(Layout)(
  M("/")
    .to("home", HomeRoute)
    .to("about", AboutRoute),
  ({ name }) => {
    switch (name) {
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
  }
);

export default App;
```
