## Context API Refactor – Detailed Changes

In this stage, the Social Media Scheduler App underwent a major structural improvement to eliminate prop-drilling and improve state management scalability. The following changes were implemented to refactor all shared state into React's Context API.


1. **Creation of PostContext.jsx**

A new file named **PostContext.jsx** was created to define and manage the shared application state. This file introduced:

  - A context object using **createContext()**.

  - A PostProvider component which encapsulates all the app’s shared state logic such as:
    - scheduledPosts – array of posts.
    - loading – loading states for API calls.
    - toast – UI feedback messages.

All core functions like addPost() and deletePost() were also defined here, along with a **useEffect** to fetch posts on mount.

The context provides a single source of truth and is used to wrap the application, making its state accessible anywhere in the component tree.

<br>

2. **Wrapping the Application with PostProvider**

In main.jsx, the entire React app was wrapped with the <PostProvider> inside <BrowserRouter>:

```
createRoot(document.getElementById("root")).render(
  <BrowserRouter>
    <PostProvider>
      <App />
    </PostProvider>
  </BrowserRouter>
);
```

This ensures that all components rendered inside <App /> have access to the context values provided by PostProvider.

<br>

3. **Creation of a Custom Hook: usePosts()**

To simplify the usage of context across multiple components, a custom hook usePosts() was created in PostContext.jsx. This hook internally uses useContext(PostContext) to return context values, and throws an error if used outside the PostProvider.

By using this hook:

- We avoid repeating useContext(PostContext) across files.
- The context usage is now clean, reusable, and encapsulated.

Example usage in a component:

```
const { addPost, loading } = usePosts();
```

<br>

4. **Refactoring Individual Components to Use Context**

Instead of passing down props from App.jsx to child components, each of the following components now directly accesses the context using usePosts():

**PostForm**

- Retrieves addPost() and loadingAtAdd from context.
- Submits new posts and handles loading spinners internally.

**PostList**

- Uses posts, deletePost(), loadingAtDelete, and error from context.
- Displays posts and allows deletion with full access to state logic.

**DetailedPost**

- Uses posts from context to fetch and render the correct post using useParams() from the URL.
- No longer needs props from its parent.

**Toast**

- Retrieves toast and setToast() from context.
- Automatically hides toast messages after a delay using useEffect.

This results in:

- Cleaner code.
- Independent, decoupled components.
- Centralized state and logic.

<br>

5. **Summary of Impact**

- The move to the Context API marks a pivotal step toward making the app more maintainable, less repetitive, and easier to scale.
- We eliminated deeply nested prop chains.
- We centralized all shared state logic for consistency.
- We made the code modular and easier to debug.
- We established a foundation for using advanced state patterns and scaling the application.

<br>
