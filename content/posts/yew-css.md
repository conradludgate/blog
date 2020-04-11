+++
title = "Yew and Dynamic Stylesheets"
date = "2020-04-11T11:06:09+01:00"
draft = true

tags = ["dev", "rust", "yew", "react", "front-end"]
+++

# Yew

I recently discovered a really cool front-end framework written in [Rust](https://rust-lang.org/) called [Yew](https://yew.rs/).
I feel like it's a spiritual cross between the functional language [Elm](https://elm-lang.org/) and [React](https://reactjs.org/).

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

Yew works very similar to React. As a demonstration, let's make a Button component in both React and Yew

#### React
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
            return <button onClick={this.handleClick}>
                {this.props.value}
            </button>
        }
    }
}
```

#### Yew
```rust
pub struct Button {
    clicked: bool,
    props: ButtonProps
    link: ComponentLink<Self>,
}

#[derive(Properties)]
pub struct ButtonProps {
    onclick: Callback<MouseEvent>,
    value: String,
}

enum Msg {
    Click(MouseEvent),
}

impl Component for Button {
    type Properties = ButtonProps;
    type Message = Msg;

    fn create(
        props: Self::Properties,
        link: ComponentLink<Self>
    ) -> Self {
        Button { clicked: false, props, link }
    }

    fn update(&mut self, msg: Self::Message) -> ShouldRender {
        match (self.clicked, msg) {
            (false, Msg::Clicked(let event)) => {
                self.props.onclick.emit(event);
                self.clicked = true;
                true
            },
            _ => false,
        }
    }

    fn view(&self) -> Html {
        if self.clicked {
            html! {
                <p>{"Clicked!"}</p>
            }
        } else {
            let onclick = self.link.callback(
                |event| Msg::Click(event)
            );

            html! {
                <button onclick=onclick>
                    {self.props.value}
                </button>
            }
        }
    }
}
```

Realistically, they're quite similar. Both examples have a type that is a component.
They both have a render function that returns HTML formatted code.
And they each have an update function.

In Yew's case, everything is a little more specific, however it's not too verbose.
It would be similar situation if you were using TypeScript with React.

This is great for me. I'm not a fan of JS but I like React. Yew let's me use the same
concepts as React but in a language I am more fond of.

## What are the downsides?

Currently, [there is no agreed upon way of styling components in Yew](https://github.com/yewstack/yew/issues/533).
Of course you can create static stylesheets and reference those classnames. However, React has shown that
dynamic stylesheets can be useful and are very popular. And systems like Less and Sass show that CSS can be a lot
more powerful than it is.

