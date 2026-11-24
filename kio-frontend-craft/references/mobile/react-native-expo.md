# React Native & Expo Router Best Practices

## 1. Project Organization Standard
```
├── app/                  # File-based routes
│   ├── (auth)/           # Unauthenticated group
│   ├── (app)/            # Authenticated protected group
│   └── +not-found.tsx    # 404 handler
├── components/           # Atomic UI elements
│   ├── ui/               # Design system primitives (Button, Input, Card)
│   └── features/         # Domain-specific components
├── hooks/                # Custom React hooks (useAuth, useDebounce)
├── services/             # API client, Supabase/Firebase connections
├── stores/               # Zustand state stores
├── utils/                # Pure formatting/helper functions
└── constants/            # Colors, typography, spacing tokens
```

## 2. Dynamic Deep Linking & Universal Links
Expo Router provides built-in deep linking. Configure in `app.json`:
```json
{
  "expo": {
    "scheme": "kioapp",
    "ios": {
      "associatedDomains": ["applinks:kio.dev"]
    },
    "android": {
      "intentFilters": [
        {
          "action": "VIEW",
          "data": [
            { "scheme": "https", "host": "kio.dev" }
          ],
          "category": ["BROWSABLE", "DEFAULT"]
        }
      ]
    }
  }
}
```

## 3. NativeWind v4 Configuration
```js
// tailwind.config.js
module.exports = {
  content: ["./app/**/*.{js,jsx,ts,tsx}", "./components/**/*.{js,jsx,ts,tsx}"],
  presets: [require("nativewind/preset")],
  theme: {
    extend: {
      colors: {
        primary: "#18181B",
        accent: "#2563EB",
        surface: "#FFFFFF",
      },
    },
  },
  plugins: [],
};
```
