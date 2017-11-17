---
layout: default
title: ReactJS controlled/uncontrolled warning
---
I was tinkering with some ReactJS code and I occasionally came across the following
warning when using form inputs.

    Input is changing a uncontrolled input of type text to be controlled

Not being very into javascript and react I thought this was something to do with how
I built my component, so spent some time paying with how I pass in the action to deal
the update etc.

Turns out it was because I assigned a `null` value to the `value` attribute of the input

Read more (here)[https://reactjs.org/docs/forms.html#controlled-input-null-value]

Controlled components are components where React determines the value of an input, not
the User as is the usual case

I naively fixed the problem like so:

Before:

    <input type=text value={this.props.buttonValue} />

After:

    <input type=text value={this.props.buttonValue || ''} />

