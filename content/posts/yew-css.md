+++
title = "Yew and Dynamic Stylesheets"
date = "2020-04-11T11:06:09+01:00"
draft = true
+++

# Yew

I recently discovered a really cool front-end framework written in [Rust](https://rust-lang.org/) called [Yew](https://yew.rs/).
I feel like it's a spiritual cross between the functional language [Elm](https://elm-lang.org/) and [React])(https://reactjs.org/).

## What's so great about it

### First, let's talk about React.

React is undeniably one of the largest front-end frameworks at the moment, 
and it's currently my first choice if I need to build a website, 
but unfortuanately it uses JavaScript (or TypeScript).

What's so bad about that? After working on front end projects professionally for a few months,
I've learnt that I really do not like working with Js. A lot of people do, and that's fine,
but I've tried and I just can't learn to love it.
There's probably an entire post about what I dislike about js so I'm not going to go into the details here.

### How does React work?

On a surface level, React works by having a 'virtual DOM' (or VDOM), a js representation of the current web page.
It then uses this VDOM to figure out which functions to run on specific events and which elements to update, if any.
If used correctly, it can and will only call functions if there's content that needs to be updated.

### Now, onto Yew

Yew is built upon Rust, which is a very modern imperitive language but with a lot of functional aspects.
It's been shown to be extremely fast, very memory efficient and very memory safe.

Yew works very similar to React. Let's make a Button component in both React and Yew

React:
```jsx
class Button extends React.Component {
    constructor(props) {
        super(props);

        this.state = {
            clicked: false,
        };

        this.handleClick.bind(this)
    }

    handleClick() {
        if (!this.state.clicked) {            
            this.props.onClick();
            this.setState({
                clicked: true,
            })
        }
    }

    render() {
        if (this.state.clicked) {
            return <p>Clicked!</p>;
        } else {
            return <button onClick={this.handleClick}>{this.props.value}</button>
        }
    }
}
```

Yew:
```rust
pub struct Button {
    clicked: bool,
    props: ButtonProps
}

#[derive(Properties)]
pub struct ButtonProps {
    onclick: Callback<ClickEvent>,
    value: String,
}

enum Msg {
    Clicked,
}

impl Component for Button {
    type Properties = ButtonProps;
    type Message = Msg;

    fn update(&mut self, msg: Self::Message) -> ShouldRender {
        match (self.clicked, msg) {
            (false, Msg::Clicked) => {
                self.onclick
            },
            (_, _) => false,
        }
    }
}
```