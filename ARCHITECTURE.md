# Clinical Feedback Assistant - Refactored Architecture

## 🎯 Overview

This application has been restructured using modern React patterns and best practices with a focus on:

- **React Context API** for global state management
- **Custom Hooks** for reusable business logic
- **TypeScript** for type safety
- **Tailwind CSS** for responsive, utility-first styling
- **Component composition** for better maintainability

## 📁 Project Structure

```
src/
├── contexts/               # React Context providers for global state
│   ├── AuthContext.tsx     # Authentication & user role management
│   ├── ConversationContext.tsx  # Chat conversations & AI messaging
│   ├── UIContext.tsx       # UI state (modals, sidebar, navigation)
│   └── index.ts           # Context exports
│
├── hooks/                 # Custom React hooks
│   ├── useAppInitialization.ts  # App initialization logic
│   ├── useMobileResponsive.ts   # Mobile-specific behaviors
│   └── useSessionSummary.ts     # Session management
│
├── types/                 # TypeScript type definitions
│   ├── index.ts          # Core app types (Message, Conversation, etc.)
│   └── summary.ts        # Summary-related types
│
├── components/            # React components
│   ├── ChatArea.tsx      # Chat message display
│   ├── RightPanel.tsx    # Main chat interface
│   ├── PermanentSidebar.tsx  # Navigation sidebar
│   ├── access/           # Authentication components
│   ├── admin/            # Admin panel components
│   └── ui/               # Reusable UI components (shadcn/ui)
│
├── api/                  # API client functions
│   ├── apiClient.tsx     # AI messaging API
│   ├── firebase.js       # Firebase configuration
│   └── supabase.ts       # Supabase configuration
│
├── utils/                # Utility functions
│   ├── session.ts        # Session ID management
│   ├── accessStorage.ts  # Access code storage
│   └── exportToPdf.ts    # PDF export functionality
│
├── App.tsx               # Main app component (simplified)
└── main.tsx              # App entry point with providers

```
# 🚀 Project Setup & Installation Guide

Follow these steps to install all dependencies and start the development
server.

------------------------------------------------------------------------

## 📦 1. Install Dependencies

Make sure you are inside the project root folder, then run:

``` bash
npm install
```

If you use **pnpm**:

``` bash
pnpm install
```

If you use **yarn**:

``` bash
yarn install
```

------------------------------------------------------------------------

## ▶️ 2. Start the Development Server

``` bash
npm run dev
```

or with **pnpm**:

``` bash
pnpm dev
```

or with **yarn**:

``` bash
yarn dev
```

The project will start on:

    http://localhost:5173

------------------------------------------------------------------------

## 🛠️ 3. Build for Production (Optional)

``` bash
npm run build
```

To preview the production build:

``` bash
npm run preview
```

------------------------------------------------------------------------

## 🎉 You're Ready to Go!

Your React + Vite + TypeScript application should now be running
successfully.



## 🏗️ Architecture Pattern

### Context-Based State Management

The application uses **three main contexts**:

#### 1. **AuthContext** (`contexts/AuthContext.tsx`)

Manages user authentication and role-based access.

**State:**

- `role`: Current user role ("preceptor" | "admin" | "user")
- `isAuthenticated`: Boolean authentication status

**Actions:**

- `login(role)`: Authenticate user with role
- `logout()`: Clear authentication and session

**Usage:**

```tsx
import { useAuth } from "./contexts";

function MyComponent() {
  const { role, isAuthenticated, login, logout } = useAuth();
  // ...
}
```

#### 2. **ConversationContext** (`contexts/ConversationContext.tsx`)

Manages all chat conversations and AI interactions.

**State:**

- `conversations`: Array of all conversations
- `activeConversationId`: Currently selected conversation
- `currentMessages`: Messages in active conversation
- `inputValue`: Current input field value

**Actions:**

- `createNewConversation(title?, standard?)`: Create new chat
- `sendMessage(message)`: Send user message to AI
- `sendStandardPrompt(type, label)`: Send standard-based prompt
- `clearConversations()`: Reset all conversations

**Usage:**

```tsx
import { useConversations } from "./contexts";

function ChatComponent() {
  const { currentMessages, inputValue, setInputValue, sendMessage } =
    useConversations();
  // ...
}
```

#### 3. **UIContext** (`contexts/UIContext.tsx`)

Manages UI state like modals, sidebar, and navigation.

**State:**

- `modals`: Object with boolean flags for each modal
- `leftSidebarCollapsed`: Sidebar visibility state
- `currentPage`: Current page ("main" | "admin")

**Actions:**

- `openModal(modalName)`: Open specific modal
- `closeModal(modalName)`: Close specific modal
- `toggleSidebar()`: Toggle sidebar visibility
- `setCurrentPage(page)`: Navigate between pages

**Usage:**

```tsx
import { useUI } from "./contexts";

function HeaderComponent() {
  const { modals, openModal, closeModal, toggleSidebar } = useUI();
  // ...
}
```

## 🎨 Component Patterns

### Composition Over Inheritance

Components are broken down into smaller, reusable pieces:

**Before:**

```tsx
// Monolithic component with everything inline
export function ChatArea({ messages }) {
  return (
    <div>
      {messages.map((msg) => (
        <div className={msg.sender === "user" ? "style1" : "style2"}>
          {/* lots of nested JSX */}
        </div>
      ))}
    </div>
  );
}
```

**After:**

```tsx
// Composed from smaller components
const Avatar = ({ sender }) => {
  /* ... */
};
const MessageBubble = ({ message }) => {
  /* ... */
};
const EmptyState = ({ onPrompt }) => {
  /* ... */
};

export function ChatArea({ messages, onSuggestedPrompt }) {
  return (
    <div className="flex-1 overflow-y-auto space-y-4 mb-4 px-2 sm:px-4 md:px-6">
      {messages.length === 0 ? (
        <EmptyState onSuggestedPrompt={onSuggestedPrompt} />
      ) : (
        messages.map((msg) => <MessageBubble key={msg.id} message={msg} />)
      )}
    </div>
  );
}
```

### Tailwind CSS Best Practices

1. **Responsive Design** - Mobile-first with breakpoint prefixes:

```tsx
className = "text-sm sm:text-base md:text-lg lg:text-xl";
```

2. **State Variants** - Hover, focus, active states:

```tsx
className =
  "hover:bg-blue-600 focus:ring-2 focus:ring-offset-2 active:scale-95";
```

3. **Component Classes** - Group related utilities:

```tsx
// Card component pattern
className = "bg-white rounded-lg p-4 shadow-sm border border-gray-200";
```

4. **Transitions** - Smooth interactions:

```tsx
className = "transition-all duration-200 hover:scale-[1.02]";
```

## 🔄 Data Flow

```
┌─────────────────────────────────────────────────────────────┐
│                         main.tsx                            │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ <AuthProvider>                                      │   │
│  │   <ConversationProvider>                            │   │
│  │     <UIProvider>                                    │   │
│  │       <App />                                       │   │
│  │     </UIProvider>                                   │   │
│  │   </ConversationProvider>                           │   │
│  │ </AuthProvider>                                     │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                         App.tsx                             │
│  - useAuth() → Get authentication state                     │
│  - useConversations() → Get chat state                      │
│  - useUI() → Get UI state                                   │
│  - useAppInitialization() → Run setup                       │
│                                                              │
│  Renders: Sidebar + ChatPanel + Modals                      │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                     Child Components                         │
│  - Access contexts via hooks                                │
│  - Trigger actions via context methods                      │
│  - Update local UI state only                               │
└─────────────────────────────────────────────────────────────┘
```

## 🚀 Benefits of This Architecture

### 1. **Separation of Concerns**

- Business logic → Contexts
- UI rendering → Components
- Type safety → Types folder
- Utilities → Utils folder

### 2. **Reusability**

- Hooks can be used in any component
- Contexts available throughout the app
- Components are composable and testable

### 3. **Maintainability**

- Clear file organization
- Single source of truth for state
- Type-safe with TypeScript
- Easy to locate and fix bugs

### 4. **Performance**

- Context prevents prop drilling
- Memoization opportunities with React.memo
- Efficient re-renders with context selectors

### 5. **Scalability**

- Easy to add new contexts
- New features don't clutter existing code
- Clear patterns for new developers

## 📝 Type System

All types are centralized in `src/types/`:

```typescript
// Core types
export type UserRole = "preceptor" | "admin" | "user";

export interface Message {
  id: string;
  content: string;
  sender: "user" | "bot";
  timestamp: Date;
}

export interface Conversation {
  id: string;
  title: string;
  preview: string;
  timestamp: string;
  messages: Message[];
  currentStandard: number | null;
}

export interface ModalState {
  progress: boolean;
  guidelines: boolean;
  privacyPolicy: boolean;
  settings: boolean;
  documentPreview: boolean;
  faq: boolean;
}
```

## 🛠️ Development Workflow

### Adding a New Feature

1. **Define types** in `src/types/index.ts`
2. **Add state/logic** to appropriate context
3. **Create hook** if needed in `src/hooks/`
4. **Build UI component** in `src/components/`
5. **Use context hooks** to access state/actions

### Example: Adding a "Favorites" Feature

```typescript
// 1. Add type
export interface Favorite {
  id: string;
  conversationId: string;
  timestamp: Date;
}

// 2. Add to ConversationContext
const [favorites, setFavorites] = useState<Favorite[]>([]);

const addFavorite = (convId: string) => {
  // logic
};

// 3. Use in component
function FavoritesPanel() {
  const { favorites, addFavorite } = useConversations();
  return (/* UI */);
}
```

## 🎓 Key Patterns Used

- **Provider Pattern**: Contexts wrap the app to provide global state
- **Custom Hooks**: Encapsulate reusable logic (useAuth, useConversations)
- **Composition**: Break components into smaller pieces
- **Controlled Components**: Form inputs controlled by React state
- **Responsive Design**: Mobile-first Tailwind CSS utilities
- **TypeScript**: Strong typing for safety and IDE support

## 📚 Further Reading

- [React Context API](https://react.dev/reference/react/useContext)
- [Custom Hooks](https://react.dev/learn/reusing-logic-with-custom-hooks)
- [Tailwind CSS Docs](https://tailwindcss.com/docs)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)

---

**Restructured with ❤️ using React best practices and Context7 documentation**
