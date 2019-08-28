# Virtual Keyboard API
An JS API to show/hide virtual keyboard and to determine its current state.


## What and why?

A virtual keyboard (also known as an on-screen keyboard or a soft keyboard)
allows text input on devices that don't have a physical keyboard, like
smartphones and tablets.  Even though a virtual keyboard is the only mechanism
for text input for most touchscreen devices, there is no JS API to
programmatically control its visibility.  There are native APIs to control
virtual keyboards, like
[`show/hideSoftInput()`](https://developer.android.com/reference/android/view/inputmethod/InputMethodManager.html#showSoftInput(android.view.View,%20int))
in Android.  The Web needs to match this capability to be competitive with
native platforms.


## Proposed API

We are proposing a two part API: a focus-based API to control virtual keyboard
visiblity, and a new `Navigator` state to check the current visibility state of
virtual keyboard.

### Controlling the visibility of virtual keyboard

Current [HTML focus
API](https://html.spec.whatwg.org/multipage/interaction.html#focus) already has
a `focusOptions` parameter.  We will have an additional field in that parameter
to control the visibility:

```javascript
// Don't show virtaul keyboard on focus:
element.focus({virtualKeyboardBehavior: "hide"});
```

This will work on any focusable element, including
[`contenteditable`](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Editable_content)
and recently proposed
[`EditContext`](https://github.com/MicrosoftEdge/MSEdgeExplainers/blob/master/EditContext/explainer.md).

Proposed IDL change:

```WebIDL
enum VirtualKeyboardBehavior { "default", "hide", "show" };

partial dictionary FocusOptions {
  VirtualKeyboardBehavior virtualKeyboardBehavior = "default";
};
```

-------------------------------------
| VirtualKeyboardBehavior | meaning |
-------------------------------------
| default | Follow default platform behavior. |
| hide    | Don't show virtual keyboard.      |
| show    | Show virtual keyboard.            |
-------------------------------------

Note that for non-programatic focus control, we already have [HTML
`inputmode`](https://html.spec.whatwg.org/multipage/interaction.html#input-modalities:-the-inputmode-attribute)
attribute.


### Checking the visibility state of virtual keyboard

To check the current visibility state of the virtual keyboard, we will have an
additional read-only attribute in
[`window.navigator`](https://developer.mozilla.org/en-US/docs/Web/API/Navigator):

```javascript
if (!navigator.virtualKeyboardVisible) {
  // Show a button to make the keyboard visible.
}
```

Proposed IDL change:

```WebIDL
partial interface Navigator {
  readonly attribute boolean virtualKeyboardVisible;
};
```
