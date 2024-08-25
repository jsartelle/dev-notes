---
{"dg-publish":true,"dg-path":"Cheat sheets/React Native cheat sheet.md","permalink":"/cheat-sheets/react-native-cheat-sheet/","tags":["language/react"]}
---


<div class="rich-link-card-container"><a class="rich-link-card" href="https://reactnative.dev/docs/getting-started" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://reactnative.dev/img/logo-og.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Introduction · React Native</h1>
		<p class="rich-link-card-description">
		This helpful guide lays out the prerequisites for learning React Native, using these docs, and setting up your environment.
		</p>
		<p class="rich-link-href">
		https://reactnative.dev/docs/getting-started
		</p>
	</div>
</a></div>

# Fundamentals

- if using the development server, shake your device to open the Developer menu (or use Cmd-D for iOS simulator and Cmd-M for Android)
- to bypass Fast Refresh and force remounting on every edit (for example, testing an intro animation) add `// @refresh reset` anywhere in the file
- to debug remotely, select *Debug JS Remotely* from the developer menu
    - React DevTools extension doesn't work, but the [standalone version](https://github.com/facebook/react/tree/main/packages/react-devtools) does
    - the component selected in the React devtools is available in the Chrome console as `$r` (make sure the Chrome console dropdown says `debuggerWorker.js`)
    - for the iOS version use the native Safari debugging under Develop -> Simulator -> JSContext
        - choose *Automatically Show Web Inspectors for JSContexts*
- display console logs by running `npx react-native log-ios` (or `log-android`)
    - Expo projects will automatically show logs in the terminal
- use `__DEV__` to check if running in dev mode

# Native Components

- `<View>` is non-scrolling, `<ScrollView>` is scrolling
- `<Text>` is for non-editable text (equivalent to `<p>`), `<TextInput>` is an editable text field
- the *accessible* prop on a component groups all its children together as one element for accessibility purposes

## Text Input

- `<TextInput>` has *onChangeText* and *onSubmitEditing* props
    - set *multiline* for multi-line input
    - use *onChangeText* to update state when the text changes
    - use *enterKeyHint* to set the label on the software Enter key to one of a few preset values
        - *enablesReturnKeyAutomatically* will disable the Enter key if no text has been entered
    - *onSubmitEditing* will fire when the Enter key (software or hardware) is pressed

## Using a ScrollView

- set *horizontal* property to allow horizontal scrolling
- *pagingEnabled* lets you use paging
    - `<ViewPager>` component can be used to swipe horizontally between views
- on iOS a `<ScrollView>` with a single item can allow the user to zoom in and out if the *minimumZoomScale* and *maximumZoomScale* props are set
- all elements are rendered even if off screen - if there are a large number of items use `<FlatList>` instead (see [[Development/Cheat sheets/React Native cheat sheet#Using List Views\|#Using List Views]])

## Using List Views

- `<FlatList>` displays a scrolling list of changing, but structurally similar data
    - *data* prop is an array of data, *renderItem* prop is a function that takes a single data item and returns the rendered component
    - number of items can change over time
    - only renders elements that are currently visible
- `<SectionList>` lets you render a list broken into sections with section headers
    - takes a *sections* prop which is an array of objects (one per section), with a *title* property (the section title) and *data* property (array of data)
    - *renderItem* prop as described above

# Design

## Layout

- style properties are written with camelCase
- all dimensions are unitless, density-independent pixels (or percent)
- `position` can be *relative* (default) or *absolute*
- all elements use flexbox, with a few differences:
    - `flex` only accepts a single number (`flex: 2` in React Native is equivalent to `flex: 2 1 0%` on the web)
    - `flexDirection` defaults to *column*
    - `alignContent` defaults to *flex-start*
    - `flexShrink` defaults to 0

## StyleSheet

- `StyleSheet.create(styleObject)` creates a stylesheet object that is reused between renders (inline styles are recreated on every render)
    - styles should be created outside of the render function

```jsx
const App = () => (
  <View style={styles.container}>
    <Text style={styles.title}>React Native</Text>
  </View>
);

const styles = StyleSheet.create({
  container: {
    flex: 1,
    padding: 24,
    backgroundColor: "#eaeaea"
  },
  title: {
    marginTop: 16,
    paddingVertical: 8,
    borderWidth: 4,
    borderColor: "#20232a",
    borderRadius: 6,
    backgroundColor: "#61dafb",
    color: "#20232a",
    textAlign: "center",
    fontSize: 30,
    fontWeight: "bold"
  }
});
```

- `StyleSheet.compose(style1, style2)` method lets you combine two StyleSheets so that the second overrides the first

```jsx
const styles1 = StyleSheet.create({
    button: {
        backgroundColor: 'blue',
        color: 'white'
    }
})

const styles2 = StyleSheet.create({
    button: {
        color: 'yellow'
    }
})

const composedStyles = StyleSheet.compose(styles1, styles2)
// creates the following:
{
    button: {
        backgroundColor: 'blue',
        color: 'yellow',
    }
}
```

- `StyleSheet.flatten(styleObjects)` takes an array of style objects and returns a flattened object that can be used in the `style` prop

```jsx
const styles1 = StyleSheet.create({
    button: {
        backgroundColor: 'blue',
        color: 'white'
    }
})

const styles2 = StyleSheet.create({
    button: {
        color: 'yellow'
    }
})

const flattenedStyle = StyleSheet.flatten([styles1.button, styles2.button])
// creates the following:
{
    backgroundColor: 'blue',
    color: 'yellow',
}
```

- `StyleSheet.absoluteFill`: a premade style object that sets `position:absolute` and `left|right|top|bottom: 0`

```jsx
<View style={[styles.background, StyleSheet.absoluteFill]}>Hello World</View>
```

- `StyleSheet.absoluteFillObject`: same as above, but returns an object that can be destructured within a `StyleSheet.create` call

```jsx
const styles = StyleSheet.create({
    background: {
        ...StyleSheet.absoluteFillObject,
        backgroundColor: 'deeppink',
    }
})
```

- `StyleSheet.hairlineWidth` - the width of a thin line on the current platform

```jsx
const styles = StyleSheet.create({
    listItem: {
        borderBottomColor: 'gray',
        borderBottomWidth: StyleSheet.hairlineWidth,
    }
})
```

## Images

- To use an image (or other asset), require it with a relative path
    - image path must be known statically

```jsx
// GOOD
<Image source={require('./my-icon.png')} />;

// BAD
var icon = this.props.active
  ? 'my-icon-active'
  : 'my-icon-inactive';
<Image source={require('./' + icon + '.png')} />;

// GOOD
var icon = this.props.active
  ? require('./my-icon-active.png')
  : require('./my-icon-inactive.png');
<Image source={icon} />;
```

- *source* can also take an object with a `uri` property - this can be a URL, asset catalog name, or `data:` URL
- Use `@2x` and `@3x` suffixes for different screen densities (ex. `my-icon@2x.png`)
- images imported with require() are automatically sized, others need to be sized manually
    - to scale a require() image dynamically, may need to manually style it with `{ width: undefined, height: undefined }`
- `<ImageBackground>` component takes the same props as `<Image>` but can have children

## Colors

- use the `PlatformColor` function to get platform-specific named colors (iOS/Android) which react to light & dark mode
    - [see here](https://reactnative.dev/docs/next/platformcolor) for valid color names
    - first value is the default, rest are fallbacks
    - doesn't work on web
    - always set a default value with [[Development/Cheat sheets/React Native cheat sheet#Platform-specific code\|Platform.select]] to handle other platforms

```jsx
const styles = StyleSheet.create({
  label: {
    padding: 16,
    ...Platform.select({
      ios: {
        color: PlatformColor('label'),
        backgroundColor:
          PlatformColor('systemTealColor'),
      },
      android: {
        color: PlatformColor('?android:attr/textColor'),
        backgroundColor:
          PlatformColor('@android:color/holo_blue_bright'),
      },
      default: { color: 'black' }
    })
  },
```

# Networking

- Fetch is available, or libraries based on XMLHttpRequest can be used
    - no CORS
- WebSockets are supported

# Touch (buttons)

- `<Button>` is a simple text-only button on iOS, and a button with colored background on Android and web
    - doesn't accept `style`, the `color` prop can be used to change the text (iOS) or background (Android/web) color
- For custom buttons, wrap a single element with a `Touchable` wrapper:
    - `<TouchableHighlight>` darkens when you tap (suitable for most buttons)
        - change the highlight color with the `underlayColor` prop
    - `<TouchableOpacity>` reduces opacity when you tap
    - `<TouchableNativeFeedback>` (Android only) does the Material Design ripple
    - `<TouchableWithoutFeedback>` provides no feedback
- `<Pressable>` is more flexible
    - supports an optional *hitSlop* property ([Rect](https://reactnative.dev/docs/rect) or number) to allow presses outside the child element
    - `style` can take a function that receives a boolean indicating if the button is currently being pressed
- `<Button>` only supports `onPress`, the others support the handlers below
    - ![Diagram of the onPress events in sequence.](https://d33wubrfki0l68.cloudfront.net/436d715612d6a5ab228b9fd41f33f799f0c3e6d3/40bdd/docs/assets/d_pressable_pressing.svg)

# Navigation

## react-navigation

- install `@react-navigation/native`, `@react-navigation/native-stack`, `react-native-screens`, `react-native-safe-area-context`
- wrap whole app in `<NavigationContainer>`

```jsx
// In App.js in a new project

import * as React from 'react';
import { View, Text } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createNativeStackNavigator } from '@react-navigation/native-stack';

function HomeScreen() {
  return (
    <View style={{ flex: 1, alignItems: 'center', justifyContent: 'center' }}>
      <Text>Home Screen</Text>
    </View>
  );
}

const Stack = createNativeStackNavigator();

function App() {
  return (
    <NavigationContainer>
      <Stack.Navigator initialRouteName="Home">
        <Stack.Screen name="Home" component={HomeScreen} options={{ title: 'Overview' }} />
        <Stack.Screen name="Details" component={DetailsScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

- `<Stack.Screen>` is a screen of your app, takes a *name* prop and a *component* prop for the page component
    - route name casing doesn't matter
    - *component* receives a prop called *navigation* with navigation methods, ex. `navigation.navigate` to go to another screen
    - to pass additional props to a screen, wrap the Navigator with a [[Development/Cheat sheets/React cheat sheet#Context \|context provider]] and access it inside the screen components

# Safe areas

- use the [react-native-safe-area-context](https://github.com/th3rdwave/react-native-safe-area-context) library (already installed in the default Expo project template)
- use `<SafeAreaView>` (which is a view with padding already applied for safe areas) instead of `<View>`, or use the `useSafeAreaInsets` hook to get the inset values

# Platform-specific code

- `Platform.OS` can be used to test for platform (`ios' | 'android' | 'windows' | 'macos' | 'web'`) for Expo

```jsx
import { Platform, StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  height: Platform.OS === 'ios' ? 200 : 100
});
```

- `Platform.select` method returns the correct input value based on the `Platform.OS` value - also supports the value `'native'` for iOS and Android

```jsx
import { Platform, StyleSheet } from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    ...Platform.select({
      ios: {
        backgroundColor: 'red'
      },
      android: {
        backgroundColor: 'green'
      },
      default: {
        // other platforms, web for example
        backgroundColor: 'blue'
      }
    })
  }
});

/* can also be used to return platform-specific components */

const Component = Platform.select({
  ios: () => require('ComponentIOS'),
  android: () => require('ComponentAndroid')
})();

<Component />;
```

- `Platform.Version` returns the Android API version number (ex. `25`) or iOS version string (ex. `'10.3'`)
- Create platform-specific imports by ending them in `.ios.js` or `.android.js`
    - Use the `.native.js` extension for files that are the same on Android and iOS, but not used on web, and use `.js` for the web version

```jsx
// imports BigButton.ios.js or BigButton.android.js depending on platform
import BigButton from './BigButton';
```

# React Native Web

- `<Text>` renders to different HTML elements based on `role` and `aria-*` props
    - Use `role="heading"` to render `<h1>`-`<h6>`
        - To change heading level use `aria-level={number}` (default is `1`)
    - To render links set the `href` prop, and use the `hrefAttrs` prop to pass an object with link attributes (`rel`, `target`, etc)
- `<View>` does the same
    - `article` and `paragraph` render their respective HTML elements
    - Use `banner` for `<header>` and `contentinfo` for `<footer>`
- `<View>` uses a flex column layout by default

> [!warning]
> Default browser styles for HTML elements are **not** applied!

```jsx
<Text role="heading">All Articles</Text>

<View role="article">
    <Text role="heading" aria-level={2}>Article Title</Text>
    <Text role="paragraph">Article text goes here.</Text>
    <Text href="https://react.dev/">Link to React</Text>
</View>
```

- Renders to:

```jsx
<h1>All Articles</h1>

<article>
    <h2>Article Title</h2>
    <p>Article text goes here.</p>
    <a href="https://react.dev/">Link to React</a>
</article>
```

# Expo

> [!info] Create a new project:
>
> ```bash
> npx create-expo-app@latest
> ```

- install [Expo Tools](https://marketplace.visualstudio.com/items?itemName=expo.vscode-expo-tools) for VSCode
- run `npx expo-doctor` to diagnose issues
- run `npx expo lint` to set up ESLint, or run it if it's already set up
- to install Prettier:
    - run `npx expo install -- --save-dev prettier eslint-config-prettier eslint-plugin-prettier`
    - add the below to `.eslintrc.js`

```js
module.exports = {
  extends: ['expo', 'prettier'],
  plugins: ['prettier'],
  rules: {
    'prettier/prettier': 'error',
  },
};
```

## Basics

- Expo has its own `<Image>` component (`npx expo install expo-image`)

## Routing & navigation

- uses [[Development/Cheat sheets/React Native cheat sheet#react-navigation\|#react-navigation]], setup is done for you
- file-based routing: all files in the `app` directory become routes
    - `app/index.tsx` is the default route
    - `app/settings/index.tsx` matches `/settings`
    - `app/settings/general.tsx` matches `/settings/general`
    - `app/settings/[page].tsx` matches any unmatched path under `/settings`, and the name is available as a route parameter via `useLocalSearchParams`

```tsx
import { useLocalSearchParams } from 'expo-router'

export default function Page() {
    const { category } = useLocalSearchParams()
}
```

- navigate using the `<Link>` component from `expo-router`
- to use a tab bar layout [see here](https://docs.expo.dev/router/advanced/tabs/)

## Page options

- to configure page options, add a `<Stack.Screen>` component to the `<Stack>` in your layout, with the *name* prop equal to the route name
    - you can set a *screenOptions* prop on `<Stack>` to set default options
    - you can also set options within a page component using the `useNavigation` hook, however, at least on web they won't take effect until the page fully loads

```jsx
import { useNavigation } from 'expo-router'

const navigation = useNavigation()

useEffect(() => {
    navigation.setOptions({ headerShown: false })
}, [navigation])
```

- change the header title with *title: 'title'*
- hide the header with *headerShown: false*
- add buttons to the header by passing a function that returns a component to the *headerLeft* or *headerRight* option
- show a route as a modal with *presentation: modal*

## Data storage

- [expo-secure-store](https://docs.expo.dev/versions/latest/sdk/securestore/) : store small (<2kb) key-value pairs with encryption

```bash
npx expo install expo-secure-store
```

```tsx
 import * as SecureStore from 'expo-secure-store';
 
 await SecureStore.setItemAsync(key, value);
 const result = await SecureStore.getItemAsync(key);
```

- [Async Storage](https://react-native-async-storage.github.io/async-storage/docs/usage): **not encrypted**, can hold larger amounts of data (~2 MB per item and 6 MB total)

```bash
npx expo install @react-native-async-storage/async-storage
```

```tsx
import AsyncStorage from '@react-native-async-storage/async-storage';

await AsyncStorage.setItem('todos', JSON.stringify({ name: 'Buy eggs', complete: false }))

const todos = await JSON.parse(AsyncStorage.getItem('todos') ?? '{}')
```

- [expo-file-system](https://docs.expo.dev/versions/latest/sdk/filesystem/): provides filesystem access
    - only have read & write access to `FileSystem.documentDirectory` (for permanent files) and `FileSystem.cacheDirectory` (for temporary files)

```bash
npx expo install expo-file-system
```

- [expo-sqlite](https://docs.expo.dev/versions/latest/sdk/sqlite/): SQLite database

```bash
npx expo install expo-sqlite
```

# MobX

- `npm install --save mobx mobx-react-lite`
    - `mobx-react-lite` only supports function components, `mobx-react` supports class components too
- the advantage over [[Development/Cheat sheets/React cheat sheet#Context\|contexts]] is that a component using a context will re-render if **anything** in that context changes, but MobX stores only cause re-render if a piece of state the component is actually using (including individual items in collections) changes
- three main concepts:
    - *state*: values you want to observe
    - *actions*: methods that modify state
    - *derivations*: anything that can be derived from the state
        - *computed values*: derived from the current state with no side effects
        - *reactions*: side effects that run whenever the state changes, should be used sparingly
- stick to [[Development/Cheat sheets/React cheat sheet#useState\|useState]] for local component state, unless you need deep watching or computed values
- grab values from objects as late as possible (close to when they're going to be rendered into the DOM)

## Creating stores

- the easiest way to set up a store is to create a class, and use `makeObservable` to "mark" the properties and actions that should be observable
- you can also use `makeAutoObservable` to make all the properties of the given object observable
    - getters will become computed values
    - you can pass overrides as the second argument, the same as `makeObservable` (pass `false` for a property to ignore it)

```js
import { action, makeObservable, observable } from 'mobx'

class TodoStore {
    todos = []

    constructor() {
        makeObservable(this, {
            todos: observable,
            addTodo: action
        })
        /* or makeAutoObservable(this) */
    }

    get incompleteTodos() {
        return this.todos.filter(t => !t.complete)
    }

    addTodo(name) {
        this.todos.push({ name, complete: false })
    }
}
export default new TodoStore()
```

- wrap React components that depend on state in `observer` to make them reactive
    - you can also use the `<Observer>` component, with the child being a render function for your component

```tsx
import { observer } from 'mobx-react-lite'

export default observer(function TodoList() {
    /* ... */
}
```

- complex apps will often have a store for UI state (which can be passed throughout the application using [[Development/Cheat sheets/React cheat sheet#Context\|Context]], and individual stores for different data domains (ex. todos, users, etc.)
- to let stores access each other, you can create a RootStore that holds references to all the other stores

```js
class RootStore {
    constructor() {
        this.userStore = new UserStore(this)
        this.todoStore = new TodoStore(this)
    }
}

class UserStore {
    constructor(rootStore) {
        this.rootStore = rootStore
    }

    getTodos(user) {
        // Access todoStore through the root store.
        return this.rootStore.todoStore.todos.filter(todo => todo.author === user)
    }
}

class TodoStore {
    todos = []
    rootStore

    constructor(rootStore) {
        makeAutoObservable(this)
        this.rootStore = rootStore
    }
}
```

## Persistent stores

- `npm install --save mobx-persist-store`

# Tamagui

## Fix "problem connecting to the react-native-css-interop Metro server"

<div class="rich-link-card-container"><a class="rich-link-card" href="https://github.com/tamagui/tamagui/issues/2279#issuecomment-1967426749" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://github.com/fluidicon.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Error when running yarn ios using starters · Issue #2279 · tamagui/tamagui</h1>
		<p class="rich-link-card-description">
		Current Behavior using npm create tamagui@latest --template expo-router and follow the steps to create a project, then run yarn ios to start development via expo go or expo-dev-client, the same err...
		</p>
		<p class="rich-link-href">
		https://github.com/tamagui/tamagui/issues/2279#issuecomment-1967426749
		</p>
	</div>
</a></div>

- in the layout, change the import of `tamagui.css` to be conditional for web only
    - this will cause FOUC, as will light/dark theme switching, so put up some kind of splash screen

```js
import { Platform } from "react-native";

if (Platform.OS === "web") {
  import("../tamagui-web.css");
}
```

# See also

- [[Development/Cheat sheets/React cheat sheet\|React cheat sheet]]
