# Input Component

## Basic Use

Many web applications require users to fill forms. HTML forms are based on the [input HTML tag](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input).

The corresponding JustPy component is `Input`.

The following program adds a text input field to a page:
```python
import justpy as jp

input_classes = "m-2 bg-gray-200 border-2 border-gray-200 rounded w-64 py-2 px-4 text-gray-700 focus:outline-none focus:bg-white focus:border-purple-500"

async def input_demo(request):
    wp = jp.WebPage()
    in1 = jp.Input(a=wp, classes=input_classes, placeholder='Please type here')
    return wp

jp.justpy(input_demo)
```

## The input Event

The program above is quite boring, it does nothing except allow you to type text into the input field. To make it more interesting, let's have what we type reflected in a paragraph on the page. For this, we need to introduce the `input` event. When you type, each character typed into the input field generates an `input` event (yes, the tag and the event are called the same name). 

Run the following program:

```python
import justpy as jp

input_classes = "m-2 bg-gray-200 border-2 border-gray-200 rounded w-64 py-2 px-4 text-gray-700 focus:outline-none focus:bg-white focus:border-purple-500"
p_classes = 'm-2 p-2 h-32 text-xl border-2'

async def my_input(self, msg):
    self.div.text = self.value

async def input_demo(request):
    wp = jp.WebPage()
    in1 = jp.Input(a=wp, classes=input_classes, placeholder='Please type here')
    in1.div = jp.Div(text='What you type will show up here', classes=p_classes, a=wp)
    in1.on('input', my_input)
    return wp

jp.justpy(input_demo)
```

The function `input_demo` creates a web page and adds an input element called `in1` to it (ignore the classes, they are there just to make the input element look nicer and do not affect the functionality of the program). 

Notice the `placeholder` attribute of `in1`. Before any text is typed into the input field or when it is emptied, the placeholder text is displayed in the field. We then define a div element that is added to the page (using the `a` keyword argument) and assigned to an attribute of `in1`. We saw this technique before. It simplifies event handling as we shall see in a second. 

Next, we bind the input event of `in1` to the function `my_input` (we could have omitted this line by adding `input=my_input` as a keyword argument when we created `in1`). `my_input` is now the input event handler for `in1`.

The input event occurs each and every time a character is typed into an input element. After every keystroke this function is run, and it changes the text of the div to the value of the input field.  By assigning the div to an `in1` attribute, we have access to all the variables we need in the event handler.

You may have noticed that there is a delay in the updating of the Div. That is because the component by default sets the `debounce` attribute of the input event to 200ms. This means an input event is generated only after a key has not been pressed for 200ms. 

Try holding the a key down and have it repeated. Only when you lift your finger will the Div update. You can set the `debounce` attribute to the value you prefer, just make sure to take into account the typing speed of your users and the latency of the connection. In general, a higher debounce value means the server will have to handle less communications and that may be an advantage for applications that need to scale.

## The Type Attribute

### Number Example

The input component can be of different [types](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#%3Cinput%3E_types) such as 'number' or  'password'.

```python
import justpy as jp

input_classes = "m-2 bg-gray-200 border-2 border-gray-200 rounded w-64 py-2 px-4 text-gray-700 focus:outline-none focus:bg-white focus:border-purple-500"
p_classes = 'm-2 p-2 h-32 text-xl border-2'

async def my_input(self, msg):
    self.div.text = self.value

async def input_demo(request):
    wp = jp.WebPage()
    in1 = jp.Input(type='number', a=wp, classes=input_classes, placeholder='Please type here')
    in1.div = jp.Div(text='What you type will show up here', classes=p_classes, a=wp)
    in1.on('input', my_input)
    return wp

jp.justpy(input_demo)
```

In the example above the type of `in1` is set to 'number'. Run the program and verify that only numbers can be input into the element.

### Color Example

The Input component also allows you to choose colors.

```python
import justpy as jp

def color_demo(request):
    wp = jp.WebPage()
    in1 = jp.Input(type='color', a=wp, classes='m-2 p-2', style='width: 100px; height: 100px', input=color_change, debounce=30)
    in1.d = jp.Div(text='Click box above to change color of this text', a=wp, classes='border m-2 p-2 text-2xl font-bold')
    return wp

def color_change(self, msg):
    self.d.style=f'color: {self.value}'
    self.d.text = f'The color of this text is: {self.value}'

jp.justpy(color_demo)
```

### Radio Button Example

In the example below we create two sets of [radio buttons](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input/radio).

Later in the tutorial we will see how to create a component for radio buttons which is a more intuitive way to use them. 

?> If you plan to use Quasar with JustPy, checkout the [QOptionGroup component](https://quasar.dev/vue-components/option-group)

```python
import justpy as jp

def radio_test():
    wp = jp.WebPage()
    genders = ['male', 'female', 'other']
    ages = [(0, 30), (31, 60), (61, 100)]

    outer_div = jp.Div(classes='border m-2 p-2 w-64', a=wp)

    jp.P(a=outer_div, text='Please select your gender:')
    for gender in genders:
        label = jp.Label(classes='inline-block mb-1 p-1', a=outer_div)
        radio_btn = jp.Input(type='radio', name='gender', value=gender, a=label)
        jp.Span(classes='ml-1', a=label, text=gender.capitalize())

    jp.Div(a=outer_div, classes='m-2')  # Add spacing and line break
    jp.P(a=outer_div, text='Please select your age:')
    for age in ages:
        label = jp.Label(classes='inline-block mb-1 p-1', a=outer_div)
        radio_btn = jp.Input(type='radio', name='age', value=age[0], a=label)
        jp.Span(classes='ml-1', a=label, text=f'{age[0]} - {age[1]}')
        jp.Br(a=outer_div)

    return wp

jp.justpy(radio_test)

```

Radio buttons respond to the 'change' event. When one is checked, JustPy automatically un-checks the other radio buttons in the group.

In the example below, the result of clicking a radio button are shown using the event handler `radio_changed`. Notice that the value of the radio button is always the same. What changes is its `checked` property. The value of a group of radio buttons is the value of the radio button in the group that is checked.

To make all the radio buttons in the group available to the event handler, when we create them, we also create a list that holds all the radio buttons in the group. We assign this list to an attribute of each radio button element (in our case `btn_list`). In the event handler we iterate over this list to report which radio button is pressed.


```python
import justpy as jp


def radio_changed(self, msg):
    self.result_div.text = ''
    d = jp.Div(a=self.result_div, classes='m-2 p-2 border')
    for btn in self.btn_list:
        if btn.checked:
            jp.Span(text=f'{btn.value} is checked', a=d, classes='text-green-500 mr-6')
        else:
            jp.Span(text=f'{btn.value} is NOT checked', a=d, classes='text-red-500 mr-6')


def radio_test():
    wp = jp.WebPage()
    genders = ['male', 'female', 'other']
    ages = [(0, 30), (31, 60), (61, 100)]

    outer_div = jp.Div(classes='border m-2 p-2 w-64', a=wp)
    # Create div to show radio button selection but don't add yet to page. It will be added at the end
    # It is created here so that it could be assigned to the radio button attribute result_div
    result_div = jp.Div(text='Click radio buttons to see results here', classes='m-2 p-2 text-xl')

    jp.P(a=outer_div, text='Please select your gender:')
    gender_list = []
    for gender in genders:
        label = jp.Label(classes='inline-block mb-1 p-1', a=outer_div)
        radio_btn = jp.Input(type='radio', name='gender', value=gender, a=label, btn_list=gender_list,
                             result_div=result_div, change=radio_changed)
        gender_list.append(radio_btn)
        jp.Span(classes='ml-1', a=label, text=gender.capitalize())

    jp.Div(a=outer_div, classes='m-2')  # Add spacing and line break

    jp.P(a=outer_div, text='Please select your age:')
    age_list = []
    for age in ages:
        label = jp.Label(classes='inline-block mb-1 p-1', a=outer_div)
        radio_btn = jp.Input(type='radio', name='age', value=age[0], a=label, btn_list=age_list,
                             result_div=result_div, change=radio_changed)
        age_list.append(radio_btn)
        jp.Span(classes='ml-1', a=label, text=f'{age[0]} - {age[1]}')
        jp.Br(a=outer_div)

    wp.add(result_div)
    return wp

jp.justpy(radio_test)
``` 

### Checkbox Example

Below is an example of a checkbox and a textbox connected using the `model` attribute

```python
import justpy as jp

def check_test():
    wp = jp.WebPage(data={'checked': True})
    label = jp.Label(a=wp, classes='m-2 p-2 inline-block')
    c = jp.Input(type='checkbox', classes='m-2 p-2 form-checkbox', a=label, model=[wp, 'checked'])
    caption = jp.Span(text='Click to get stuff', a=label)

    in1 = jp.Input(model=[wp, 'checked'], a=wp, classes='border block m-2 p-2')
    return wp

jp.justpy(check_test)
```

See what happens when you clear the text input element.

## Your First Component

JustPy allows building your own reusable components. We will have a lot more to say about this later, but just to start easing into the subject, let's suppose we want to encapsulate the functionality above into one component. The program would look like this:

```python
import justpy as jp

class InputWithDiv(jp.Div):

    @staticmethod
    def input_handler(self, msg):
        self.div.text = self.value

    def __init__(self, **kwargs):
        super().__init__(**kwargs)

        input_classes = "m-2 bg-gray-200 border-2 border-gray-200 rounded w-64 py-2 px-4 text-gray-700 focus:outline-none focus:bg-white focus:border-purple-500"
        self.in1 = jp.Input(a=self, classes=input_classes, placeholder='Please type here', input=self.input_handler)
        self.in1.div = jp.Div(text='What you type will show up here', classes='m-2 p-2 h-32 text-xl border-2', a=self)


async def input_demo(request):
    wp = jp.WebPage()
    for i in range(10):
        InputWithDiv(a=wp)
    return wp

jp.justpy(input_demo)
```

Try running the program. It will put on the page 10 pairs of input and div elements. If you type into the respective input field, the text will show up in the respective div.

JustPy components are Python classes that inherit from JustPy classes. In the example above, we define the class `InputWithDiv` which inherits from the JustPy class `Div`. The class constructor adds an input element and another div to the basic div, with the appropriate functionality. Now, we have a component, `InputWithDiv`, that we can reuse as we please in any project. 

If you don't completely understand what is going on, don't worry. We will revisit this in much more detail later. The take home message at this stage is that the way you build complex applications in JustPy is by building components with isolated functionality. Hopefully, if  JustPy gains popularity, there will be many components that the community will develop and share. 