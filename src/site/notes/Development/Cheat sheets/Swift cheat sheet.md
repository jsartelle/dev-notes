---
{"dg-publish":true,"dg-path":"Cheat sheets/Swift cheat sheet.md","permalink":"/cheat-sheets/swift-cheat-sheet/"}
---


# Swift

- Backticks around identifiers are ignored, and allow you to use reserved words as identifiers (ex. `` `default` ``)
- Closure arguments can be accessed using $0, $1, etc.
- \\typename.path represents a [key path](https://docs.swift.org/swift-book/ReferenceManual/Expressions.html#grammar_key-path-expression) to a property
    - typename can be omitted if it can be inferred
    - path can include multiple periods, subscripts (if parameter conforms to Hashable), can use optional chaining or force unwrapping
    - can use a key path expression whose root type is SomeType and whose path produces a value of type Value, instead of a function or closure of type (SomeType) -> Value
- separate multiple conditions in if statements using , (instead of &&)
- Create a dictionary which groups items by the results of a closure:

```swift
let students = ["Kofi", "Abena", "Efua", "Kweku", "Akosua"]
let studentsByLetter = Dictionary(grouping: students, by: { $0.first! })
// ["E": ["Efua"], "K": ["Kofi", "Kweku"], "A": ["Abena", "Akosua"]]
```

# SwiftUI

## Model/State

- Create a model object that inherits from ObservableObject to hold reactive state that views can share
    - `import Combine`
    - An observable object needs to publish any changes to its data using the @Published attribute, so that its subscribers can pick up the change
        - if a property will never change, no need to add @Published
    - Use the @StateObject attribute when initializing the model object (ex. in the App instance) to ensure it is initialized only once during the life time of the app
        - `@StateObject private var modelData = ModelData()`
    - use the .environmentObject modifier to pass the state object down to child views (in the App instance or in previews)
        - `.environmentObject(ModelData())`
    - use the @EnvironmentObject attribute in child views to receive the state object
        - `@EnvironmentObject var modelData: ModelData`
- Use state properties (marked with @State) to hold information that’s specific to a view and its subviews
    - always create state as private
- By prefixing a state (or state object) variable with $, you pass a binding which can be updated by the view it's passed into
- Use @Binding on a view property to propagate changes upwards

```swift
/* LandmarkDetail.swift */
@EnvironmentObject var modelData: ModelData
...
FavoriteButton(isSet: $modelData.landmarks[landmarkIndex].isFavorite)

/* FavoriteButton.swift */
@Binding var isSet: Bool
```

## Views

- To change alignment in Text view:

```swift
Text("Hello world")
    .frame(width: 100, alignment: .leading)
```

- `Group` can be used to apply modifiers to a bunch of child views
- `ForEach` renders a bunch of views individually without creating a new container
    - To use `ForEach` on an array and get the index:

```swift
ForEach(Array(zip(items.indices, items)), id: \.1) { index, item in
    ...
}
```

- When iterating using `List`, `ForEach`, etc, must provide a keypath for a unique id unless the objects being iterated over implement Identifiable

## Learn More

<div class="rich-link-card-container"><a class="rich-link-card" href="https://twitter.com/jsngr/status/1495061809694449664" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Jordan Singer on Twitter</h1>
		<p class="rich-link-card-description">
		Learning SwiftUI unleashed creativity inside of me like no language ever has and brought joy back to building user interfaces.Here's a thread of my experiments to show you what's possible, to learn from, and code to remix👇 pic.twitter.com/vK0U6NZRLW— Jordan Singer (@jsngr) February 19, 2022
		</p>
		<p class="rich-link-href">
		https://twitter.com/jsngr/status/1495061809694449664
		</p>
	</div>
</a></div>

<div class="rich-link-card-container"><a class="rich-link-card" href="https://github.com/jordansinger/messages-multiplatform-swiftui-sample" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://opengraph.githubassets.com/1ae109301a3e1b448b2613d8a6948ab038acdc13bccbb3a06b0dfd88f3ddf4b2/jordansinger/messages-multiplatform-swiftui-sample')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">GitHub - jordansinger/messages-multiplatform-swiftui-sample: Multiplatform Messages app for macOS, iOS, iPadOS in SwiftUI</h1>
		<p class="rich-link-card-description">
		Multiplatform Messages app for macOS, iOS, iPadOS in SwiftUI - GitHub - jordansinger/messages-multiplatform-swiftui-sample: Multiplatform Messages app for macOS, iOS, iPadOS in SwiftUI
		</p>
		<p class="rich-link-href">
		https://github.com/jordansinger/messages-multiplatform-swiftui-sample
		</p>
	</div>
</a></div>
