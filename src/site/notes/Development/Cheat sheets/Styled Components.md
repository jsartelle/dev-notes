---
{"dg-publish":true,"dg-path":"Cheat sheets/Styled Components.md","permalink":"/cheat-sheets/styled-components/"}
---


<div class="rich-link-card-container"><a class="rich-link-card" href="https://styled-components.com/" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://www.styled-components.com/atom.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">styled-components</h1>
		<p class="rich-link-card-description">
		Visual primitives for the component age. Use the best bits of ES6 and CSS to style your apps without stress 💅🏾
		</p>
		<p class="rich-link-href">
		https://styled-components.com/
		</p>
	</div>
</a></div>

- lets you attach styles to components using template strings
    - style rules can be nested
- styled components should be defined **outside of the render function**, or they will be recreated on every render

```jsx
import styled from 'styled-components'

const Header = styled.h1`
    font-size: 1.5em;
    color: deepskyblue;

    &::before {
        content: '# ';
    }

    span {
        color: red;
    }
`

export default function Page() {
    return (
        <Header>Hello world! <span>This text will be red</span></Header>
    )
}
```

- to extend an existing component's styles, pass it to the `styled` constructor

```jsx
const Button = styled.button`
    background-color: gray;
    color: white;
`

const PrimaryButton = styled(Button)`
    background-color: darkblue;
`
```

- you can use other styled components in selectors with interpolation (only on the web, not React Native)

```JSX
const Link = styled.a`
    /* styles */
`

const Icon = styled.svg`
    fill: black;

    ${Link}:hover & {
        fill: blue;
    }
`
```

- you can access props on the styled component using interpolated functions

```jsx
const Button = styled.button`
    background-color: ${props => props.primary ? 'deepskyblue' : 'white'};
`

export default function Popup() {
    return (
        <Button>Cancel</Button>
        <Button primary>OK</Button>
    )
}
```

- you can pass props to the component being wrapped using `styled.attrs`
- to make a styled component render with a different tag, use the `as` prop

```jsx
const Header = styled.h1`
    color: gray;
`

const SubHeader = styled(Header).attrs(props => ({
    as: 'h2'
}))`
    color: darkgray;
`

export default function Page() {
    return (
        <Header>Heading 1</Header>
        <SubHeader>Heading 2</SubHeader>
        <SubHeader as="h3">Heading 3</SubHeader>
    )
}
```
