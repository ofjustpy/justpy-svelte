# HTML Components

## Introduction

In JustPy components are Python classes. All HTML components inherit from either the Div or Input component. The Input component itself inherits from the Div component. 

The reference will describe primarily the Div component and the Input component.

It would be helpful to read the [HTML Components](tutorial/html_components) chapter in the tutorial first.

## Div Component

### Introduction

The Div component is the basic container component of JustPy. It is a component that can contain other components. It can of course, contain components that themselves are container components.


### Attributes
---

#### General

Attributes can be set as keywords when an element is created.
```python
import justpy as jp

jp.Div(text='hello', classes='text-red-500')
```

They can also be set anytime after the element is created
```python
import justpy as jp

d = jp.Div()
d.text = 'hello'
d.classes= 'text-red-500'
```

#### text
 
 * Type: `string`
 * Default: `""`  (the empty string)
 
The `text` attribute will always be rendered as the first child of the component. If you want a Div (or any container component) instance to render multiple texts in different locations, encompass the text in another container component and add that component to the Div at the location you want.

If you want to render an [HTML Entity](https://developer.mozilla.org/en-US/docs/Glossary/Entity) make sure it is the only text set in that container element. Otherwise, it will not be rendered correctly. The simplest way to handle entities correctly, is to use the Entity component:

```python
import justpy as jp


def entity_test():
    wp = jp.WebPage()
    jp.Entity(entity='a&#768;',a=wp, classes='text-lg')
    jp.Entity(entity='a&#769',a=wp, classes='text-xl')
    jp.Entity(entity='&#8707;',a=wp, classes='text-2xl')
    jp.Entity(entity='&copy;',a=wp, classes='text-3xl')
    return wp

jp.justpy(entity_test)
```

#### classes

 * Type: `string`
 * Default: `""`  (the empty string)
 
The classes to attach to the element. Usually Tailwind classes, but may be any classes you define. Basically equivalent to the [`class`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/class) attribute of HTML tags.

```python
import justpy as jp

def classes_test():
    wp = jp.WebPage()
    d = jp.Div(text='Classes Example', a=wp)
    # Assign Tailwind classes to d
    d.classes = 'text-5xl text-white bg-blue-500 hover:bg-blue-700 m-2 p-2 w-64'
    return wp

jp.justpy(classes_test)
```

#### style

 * Type: `string`
 * Default: `""`  (the empty string)
 
The CSS to attach to the element. Basically equivalent to the [`style`](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/style) attribute of HTML tags.

```python
import justpy as jp

def style_test():
    wp = jp.WebPage()
    for size in range(1,101):
        jp.Div(text=f' {size}', style=f'font-size: {size}px; color: red', a=wp, classes='inline cursor-pointer', size=size,
               click='self.size *= 2; self.style=f"font-size: {self.size}px; color: green";')
    return wp

jp.justpy(style_test)
```

#### components
 
 * Type: `list`
 * Default: `[]`
 
Holds the list of child elements in the Div. Strictly speaking these are not components, they are elements, instances of components. When the Div is rendered, the elements are rendered according to their place on the list, starting with the first.


In the following example, the Div elements will show up in a different order each time the page is loaded because `main_div.components` is shuffled.

```python
import justpy as jp
import random

def shuffle_test():
    wp = jp.WebPage()
    main_div = jp.Div(classes='flex flex-wrap m-2 p-2 ', a=wp)
    for i in range(1,101,1):
        jp.Div(text=f'Div {i}', a=main_div, classes='m-2 p-2 text-xl text-white bg-blue-500')
    random.shuffle(main_div.components)
    return wp


jp.justpy(shuffle_test)
```

#### events
 
 * Type: `list`
 * Default: `[]`

When an element is bound to an event, the event name (of type string) is added to `events`. You can use `events` to check which events the element will respond. Also, by removing an event name from the list, you will disable the element responding to that event.

#### allowed_events
 
 * Type: `list`
 * Default: `['click', 'mouseover', 'mouseout', 'mouseenter', 'mouseleave', 'input', 'change', 'after', 'before', 'keydown', 'keyup', 'keypress']`

If an event name is not in `allowed_events`, JustPy will generate an error if you try to bind to that event.
You can use the `add_event` method to add an allowed event to the list.

#### inner_html

 * Type: `string`
 * Default: `""`  (the empty string)
 
Used to to set the HTML of an element directly.

```python
import justpy as jp

def inner_html_test():
    wp = jp.WebPage()
    for i in range(1,11):
        jp.Div(inner_html=f'<span style="color: orange">{i}) Hello!</span>', a=wp, classes='m-2 p-2 text-3xl')
    return wp

jp.justpy(inner_html_test)
```


!> If `inner_html` is not the empty string, it will override any other content of the element

#### show

 * Type: `boolean`
 * Default: `True`
 
If set to `False`, the element is not rendered. See [here](tutorial/html_components?id=showing-and-hiding-elements)


#### animation

 * Type: `string`
 * Default: `""`  (the empty string)
 
Set the animation of the element. Uses [animate.css](https://daneden.github.io/animate.css/) 

Control the speed of the animation by adding classes:

| Class Name | Speed Time |
| ---------- | ---------- |
| `slow`     | `2s`       |
| `slower`   | `3s`       |
| `fast`     | `800ms`    |
| `faster`   | `500ms`    |

For more information and additional options consult the [animate.css documentation](https://github.com/daneden/animate.css/blob/master/README.md)

```python
import justpy as jp
import random

input_classes = "m-2 bg-gray-200 appearance-none border-2 border-gray-200 rounded w-64 py-2 px-4 text-gray-700 focus:outline-none focus:bg-white focus:border-purple-500"


def animate(self, msg):
    self.d.delete_components()  # remove all components from d
    directions = ['Up', 'Down', 'Left', 'Right']
    for letter in self.text_to_animate.value:
        letter = '&nbsp;' if letter == ' ' else letter  # Hard space for HTML, otherwise space ignored
        jp.Div(animation=f'fadeIn{random.choice(directions)}', text=letter,
               classes='rounded-full bg-blue-500 text-white text-6xl', a=self.d, temp=True)


def animation_test():
    wp = jp.WebPage()
    text_to_animate = jp.Input(a=wp, classes=input_classes, placeholder='Enter text to animate', value='Animation Demo!', input='return True')
    animate_btn = jp.Button(text='Animate!', click=animate, classes='w-32 m-2 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded', a=wp)
    animate_btn.text_to_animate= text_to_animate
    d = jp.Div(classes='flex items-center justify-center', style='height: 500px', a=wp)
    animate_btn.d = d
    return wp

jp.justpy(animation_test, websockets=False
```


#### id and name
 
 * Type: `string`
 * Default: Nothing assigned
 
 If you need to identify an element use the `name` attribute, NOT the `id` attribute. JustPy assigns a unique id to elements that are associated with events and uses it to identify which event handler to run.
 
 !> It is advised no to change an element's `id` or assign it an id. Use `name` instead. 
 

#### delete_flag

 * Type: `boolean`
 * Default: `True`
 
If set to `False`, element is not deleted when a page which it is on closes. In addition, non of its child components will be deleted.
If you create an element that will be used on more than one page, set the value to `False`.

#### event_propagation

 * Type: `boolean`
 * Default: `True`
 
The current version of JustPy supports only event bubbling. Events originate in the innermost child and bubble up through the parents unless`event_propagation` is set to `False`.


```python
import justpy as jp

def event_propagates():
    wp = jp.WebPage()
    main_div = jp.Div(classes='flex flex-wrap m-2 p-2 ', a=wp, click='self.text="main div clicked"')
    for i in range(1,10):
        jp.Div(text=f'Div {i}', a=main_div, classes='m-2 p-2 text-xl text-white bg-blue-500', click='self.text="clicked"')
    return wp

@jp.SetRoute('/no_propagation')
def event_does_not_propagate():
    wp = jp.WebPage()
    main_div = jp.Div(classes='flex flex-wrap m-2 p-2 ', a=wp, click='self.text="main div clicked"')
    for i in range(1,10):
        jp.Div(text=f'Div {i}', a=main_div, classes='m-2 p-2 text-xl text-white bg-blue-500',
               event_propagation=False, click='self.text="clicked"')
    return wp

jp.justpy(event_propagates)
```

#### vue_type

 * Type: `string`
 * Default: `'html_component'`
 
The Vue component to couple with the Python class. Used by the front end of the framework.

!> If you change this, the component will not be rendered correctly. 

?> When you develop components that require a Vue component as well, you will need to set this attribute in the Python class

#### html_tag

 * Type: `string`
 * Default: `'div'`
 
The HTML tag that corresponds to the component. If you change it, the component may no render correctly.

!> If you change this, the component will not be rendered correctly.

#### class_name

 * Type: `string`
 * Default: `'Div'`
 
The class (component) name of the element.
 
#### Global HTML attributes

Div supports some HTML global attributes. Please review the  [Common Attributes](tutorial/html_components?id=common-attributes) section in the Tutorial 

### Methods

def delete(self):

def on(self, event_type, func):

def remove_event(self, event_type):

def has_event_function(self, event_type):

async def update(self):

remove_page_from_pages(self, wp: WebPage):

def add_page(self, wp: WebPage):   def add_page_to_pages(self, wp: WebPage):

def set_model(self, value):


async def run_event_function(self, event_type, event_data, create_namespace_flag=True):

@staticmethod
    def convert_dict_to_object(d):

HTML Base Component

def initialize(self, **kwargs):

def __len__(self):

def __repr__(self):

def add_to_page(self, wp: WebPage):

    def add_to(self, *args):
        for c in args:
            c.add_component(self)

def add_attribute(self, attr, value):

def add_event(self, event_type):  def add_allowed_event(self, event_type):

def add_scoped_slot(self, slot, c):

def to_html(self, indent=0, indent_step=0, format=True):

def react(self, data):

def convert_object_to_dict(self):  # Objects may need redefine this


Div component

def delete(self):

def add_component(self, child, position=None, slot=None):

def delete_components(self):


def add(self, *args):

def add_first(self, child):


def remove_component(self, component):

def get_components(self):


def first(self):

def last(self):

def to_html(self, indent=0, indent_step=0, format=True):


def model_update(self):

def convert_object_to_dict(self):



## Input Component

### Attributes

### Methods