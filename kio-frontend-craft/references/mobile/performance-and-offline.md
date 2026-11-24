# Performance, Offline Sync & Store Publishing

## 1. Zero Memory-Leak Checklist
- Always clean up event listeners (`AppState.addEventListener`, `Keyboard.addListener`) in `useEffect` returns.
- Avoid passing new object literals or functions as props in looped child components.
- Profile memory heap using Flipper or React Native DevTools performance profiler.

## 2. TanStack Query + AsyncStorage Persistence
```tsx
import { QueryClient } from '@tanstack/react-query';
import { createAsyncStoragePersister } from '@tanstack/query-async-storage-persister';
import AsyncStorage from '@react-native-async-storage/async-storage';

export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      gcTime: 1000 * 60 * 60 * 24, // 24 hours
      staleTime: 1000 * 60 * 5,    // 5 minutes
    },
  },
});

export const asyncStoragePersister = createAsyncStoragePersister({
  storage: AsyncStorage,
});
```

## 3. Store Submission Checklist
- [ ] iOS App Privacy report declared (tracking, permissions).
- [ ] Adaptive icons, splash screen assets configured for all DPIs.
- [ ] Build number auto-incremented in `app.json` or EAS CLI.
- [ ] Proguard / Hermes minification enabled on Android release APK/AAB.
