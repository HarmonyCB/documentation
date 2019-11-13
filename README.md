# Basic concepts

Welcome to HarmonyCB, the easiest way to create a User Interface (UI) to interface with your backend Admin endpoints.

Our philosophy is simple. We want to create Harmony (see what we did there) between your backend and your tasks with the use of a smooth, straightforward UI connecting to your product.

The UI definition language is XML and is similar to a very simplified version of HTML, where layout decisions are made sensibly and automatically. In addition, we provide a simplified set of elements that we call **Ingredients** -  things like `<edit>` boxes for text and `<dateinput>` fields for times and dates.

    <markdown>*Hello World*</markdown>

The UI definition is, of course, hierarchical:

    <group title="Address">
        <edit title="Street"/>
        <edit title="Town"/>
    </group>


The second concept is that of **Data DOM** and **bindings**, which is how HarmonyCB ties the UI to your data.

Alongside the UI, every page in HarmonyCB has a single Javascript object attached to it, from which  data is taken and to which data is saved when users update the UI. Connecting this object, and the UI hierarchy is done via *bindings*.

For example, if the data has the following structure

    {
        users:[
            {
                name:"Fred",
                address: {
                    street: "mystreet",
                    town: "mytown",
                }
            },

            { ... }
        ]
    }


one could write the following UI to edit this:

    <table binding="users">
        <edit binding="name">
        <group binding="address">
            <edit binding="street">
            <edit binding="town">
        </group>
    </table>


This is basically all you have to do when creating a UI for this data structure! Of course, not all data structures match the UI quite as well as this one, so one could also use standard dot notation to connect up the UI.

For example:

    <table binding="users">
        <text binding="address.street">
        <text binding="address.town">
    </table>

This should give you a reasonable idea on how the system of the UI is bound to this Data DOM object.

Next question is, how do we **retrieve or send this data to and from the server?**

With most requests (like pressing a button), this whole Data DOM will be sent as the request body (or query parameters), allowing your backend to both populate a UI, as well as update the internal/database state from user changes to the UI.

Not ALL data in the Data DOM will make sense for an endpoint to receive, and not all data sent from the results of an endpoint will make sense to display. However, for the sake of simplicity we transmit and receive all of it. Naturally, it will be possible to restrict / reformat this data should a use case be required.

# Ingredients
//Cat: Add screenshot to each Ingredient to show what it would result in.

Here is a list of all possible Ingredients. There's plenty of samples to get you started too!

## action

The action Ingredient represents a button in the UI. This button will call an endpoint, passing the Data DOM as either the body, or flattened as the query depending on query type. Once the endpoint returns, it will open a new page underneath the current one, with the data returned in the body of the response.

### Examples

This action will open the page search_display passing the current Data DOM to the endpoint, and populating the new page Data DOM with the results.

    <action title="Search"
            page="search_display"
            method="GET"
            url="https://my.app.com/api/search"/>

### Attributes

Here are the attributes usable on the Ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| title | `title="Go"`          | This is the text label of the button |
| page  | `page="show_results"`          | The ID of the page to display the endpoint results |
| method  | `method="POST"`          | The HTTP method to use in order to run the request |
| url  | `url="http://my.app.io/api/details"`          | The endpoint of the request |


## actions
The actions Ingredient is used as a group for all `<actions>`. When a new action is performed from the group, the results page will reset and the new action will be used.


//Cat: maybe we should call "action" "button" instead.. otherwise this might be confusing. dont you think?

### Examples
This action opens the page search_display passing the current Data DOM to the endpoint, and populating the new page Data DOM with the results.

    <actions>
        <action title="Go to page one" page="page_one"/>
        <action title="Go to page two" page="page_two"/>
    </actions>

### Attributes

There are no additional attributes for the `<actions>` tag.

## checkbox
This Ingredient represents a boolean value.

### Examples


    <checkbox binding="isPremium" title="Premium User?"/>

### Attributes

Here are the attributes usable on the Ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| title | `title="Has Cake?"`          | This is the text label of the checkbox |
| binding  | `binding="cakeValue"`          | The databinding of this Ingredient |


## edit
This Ingredient represents an editable string value.

### Examples


    <edit binding="firstName" title="First Name"/>
This example has a default value:

    <edit binding="firstName" title="First Name">Matt</edit>
This example won't show a title, but a placeholder text in case the edit ingredient is empty.

    <edit binding="firstName" placeholder="Enter your first name"/>

### Attributes

Here are the attributes usable on the Ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| title | `title="Star Sign"`          | This is the text label of the edit |
| binding  | `binding="starSign"`          | The databinding of this &lt;edit&gt; ingredient|
| placeholder  | `placeholder="Enter your starsign"`          | The value to display / greyed out when empty |



## editlist
This Ingredient represents an editable array of string values. These values are a comma separated list which is turned into / read from an array on the data binding node.

### Examples

    <editlist binding="emails" title="Email Addresses">matt@example.com, test@example.com</editlist>

### Attributes

Here are the attributes usable on the Ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| title | `title="Names"`          | This is the text label of the edit list |
| binding  | `binding="names"`          | The databinding of this edit list |
| placeholder  | `placeholder="Enter a list of comma seperated names"`          | The value to display / greyed out when empty |

## endpoint
The endpoint Ingredient fetches data from an endpoint and places it on the Data DOM.

### Examples


    <endpoint binding="results"
              method="GET"
              url="https://www.example.com/api/example"/>

### Attributes

Here are the attributes usable on the Ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| binding  | `binding="names"`          | The databinding where the data will go |
| method  | `method="POST"`          | The HTTP method to use for this endpoint |
| url  | `url="https://www.example.com/api/names"`          | The URL of the endpoint |

## group
This Ingredient groups together other Ingredients with an optional title. It can also arrange the grouped items as a column (default) or as a row. Group is probably the main way one can affect the automatic layouting in HarmonyCB.

### Examples

A group of two edit boxes arranged vertically:

    <group binding="address" title="Address">
        <edit binding="line1"/>
        <edit binding="line2"/>
    </group>

A group of two edit boxes arranged horizontally:

    <group binding="name" row="true" title="Name">
        <edit binding="first" title="First"/>
        <edit binding="last" title="Last"/>
    </group>

### Attributes

Here are the attributes usable on the Ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| title | `title="Names"`          | This is the text label of the group |
| binding  | `binding="names"`          | The databinding of the group. Oftentimes it useful to insert intermediate layers in a complex Data DOM |
| row  | `row="true"`          | If present and value is "true", it will arrange items horizontally |

## html
Inserts HTML formatted text into your UI. Normally used for page instructions, although it can also be data-bound to take sanitized HTML from the Data DOM.

Note: HTML inside XML must be escaped!

### Examples

Display a simple instructional text:

    <html>&lt;h1&gt;Instructions&lt;/h1&gt;
    Feel free to fill out the form below and click the send button.
    </html>


### Attributes

Here are the attributes usable on the Ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| binding  | `binding="names"`          | The databinding for taking the content |

## iconaction
This Ingredient is an action. Instead of a button, it will display a font-awesome icon. One can use any name from the free font-awesome [icon set](https://fontawesome.com/) in the project. This is normally used to represent actions for rows on a table.

//Cat: pro feature: more icons?? ;)

### Examples

A table of users with an "add to address book" icon action:

    <table binding="users">
        <text binding="name"/>
        <iconaction icon="address-card" method="POST" url="example.com/addressbook/add">
    </table>

A table of users with multiple icon actions in one column:

    <table binding="users">
        <text binding="name"/>
        <group title="Actions">
            <iconaction icon="cloud-download" .../>
            <iconaction icon="cloud-uplode" .../>
        </group>
    </table>

### Attributes

Here are the attributes usable on the Ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| title | `title="Upload"`          | The text label of the iconaction |
| icon | `icon="cloud-upload"` | The font-awesome icon name. NOTE: this can optionally be preceded with one of the font awesome icon groups "fas,icon-name" "far,icon-name" or "fab,icon-name"
| method | `method="GET"` | The HTTP method to use when the icon is clicked
| url | `url="example.com/upload"` | The HTTP URL to use when the icon is clicked

## if
This Ingredient will act as a conditional, only creating child UI elements on a certain condition. At present the only condition is whether a child of the binding Data DOM exists or not.

### Examples

This will only create the text Ingredient if the property "user" exists on the current data binding:

    <if exists="user">
        <text binding="user.name"/>
    </if>

### Attributes

Here are the attributes usable on the Ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| exists | `exists="error"`          | The property to check exists in the data bindings. If so it will create the child ingredients |

## image
This Ingredient will insert an image into your UI, which can be used anywhere (including in tables). Optionally it takes the width and/or height. If only one image is present it is uniformly scaled along the other axis keepngs its aspect ratio.

### Examples

Display an image fullsize:

    <image>https://example.com/example.jpg</image>


Display an image where the URL is taken from the databinding property with a width of 256 pixels and a height that keeps the aspect ratio the same as the original:

    <image binding="thumbnailURL" width="256"/>

### Attributes

Here are the attributes usable on the Ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| binding  | `binding="names"`          | The databinding for the image URL |
| width  | `width="128"`          | The final width of the imagef not present, width is calculated either from the original width or with respect to the height specified |
| height  | `height="128"`          | The final height of the image. I, if not present calculated similar to width |


## json
This Ingredient will insert a representation of the current Data DOM into the UI, whi is used for debugging.

### Examples

    <json/>

## markdown
Markdown inserts markdown formatted text into your UI. It is used for page instructions. It can also be data-bound to take the markdown text from the Data DOM.

### Examples

Display a simple instructional text:

    <markdown>**Instructions**
    Feel free to fill out the form below and click the send button.
    </markdown>

Display text from an endpoint that returns:

	{
		"text":"This is a *test*."
	}

use:

    <markdown binding="text"/>

to display: "This is a *test*."

### Attributes

Here are the attributes usable on the Ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| binding  | `binding="names"`          | The data-binding from which the markdown content is read. |

## message
Message is an instructional message bar to inform the user that actions have succeeded or failed. If given a data-binding, it will only display its contents if its data-binding property actually exists. This can be used to only display the text in the case that certain criterion are met, without having to use an `<if>` ingredient.

If the type attribute is left out it will display as a plain white information bar. This is also controlled by existence of its bound data.

### Examples

Display error, warning, or success messages depending  whether the bindings exist on the returned data.

    <message binding="error" type="error"/>
    <message binding="warning" type="warning"/>
    <message binding="success" type="success"/>

Display an informational hard coded message bar:

    <message>Hi there! Have some information!</message>

### Attributes

Here are the attributes usable on the Ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| binding  | `binding="something"`          | The databinding for taking the content, and determining visibility |
| type  | `type="success"`          | Optional type of the message. Valid values: "error", "warning" and "success" |


## multiline
This Ingredient represents an editable string value, and it can used as a drop in for `<edit>` when multiple lines are required.

### Examples

    <multiline binding="comments" title="Comments"/>

### Attributes

Here are the attributes usable on the ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| title | `title="Description"`          | This is the text label of the edit |
| binding  | `binding="description"`          | The databinding of this editbox |
| placeholder  | `placeholder="Enter a description here"`          | The value to display / greyed out when empty |
| height  | `height="256"`          | Optional initial height to render the multiline text box. U, used if the default is too small |

## pagelink

This is a text element that links to another page in HarmonyCB. The text is taken from either the XML or the databinding. The new page is opened with a query parameter, set to the value of the text (this parameter is called: `_value`).
Since all query parameters end up in the Data DOM of the linked-to page it can be used to influence the new page.

It is used to link ID's or primary keys in tables to a single item edit page.

### Examples

    <table binding="users">
        <pagelink binding="id" title="ID" page="single_user"/>
        <text binding="name" title="Name"/>
    </table>

### Attributes

Here are the attributes usable on the Ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| title | `title="ID"`          | This is the text label of the link |
| binding  | `binding="id"`          | The databinding of this link |
| page  | `page="edit_stuff"`          | The page to move to ||

## *replicate
This is a special ingredient that assumes the binding is an array object in the Data DOM, and creates a single instance of each of it's children, setting *their* binding to be the corresponding array element from which they were created.

It is often used to take result data from a table, and create a row for each in the UI.

Note: Most use cases will be better off using a `<table>` element.

### Examples

    <replicate binding="users">
        <pagelink binding="id" title="ID" page="single_user"/>
        <text binding="name" title="Name"/>
    </replicate>

For example, if your Data DOM has the following

		{
			items: [
				{name:"Mary"},
				{name:"Jane"},
				{name:"Jessica"}
			]
		}

and your UI is:

	<replicate binding="items">
		<text binding="name"/>
	</replicate>

The result would be three text fields displaying "Mary", "Jane" and "Jessica" respectively.

### Attributes

Here are the attributes usable on the Ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| title | `title="ID"`          | This is the text label of the link |
| binding  | `binding="id"`          | The databinding of this link |

## *table
This is a special Ingredient. It assumes the binding is an a array object in the Data DOM. It creates a single instance of each of it's children, placing them inside a table and setting their binding to be the corresponding array element from which they were created.

Should the direct descendant child element have a title attribute, this title element is used as the heading of the column, rather than displayed with the item.

### Examples

    <table binding="users">
        <pagelink binding="id" title="ID" page="single_user"/>
        <text binding="name" title="Name"/>
    </table>

For example, if your Data DOM has the following

		{
			items: [
				{name:"Mary", age:34},
				{name:"Jane", age:75},
				{name:"Jessica", age:12}
			]
		}

and your UI is:

	<table binding="items">
		<text binding="name" title="First Name"/>
		<text binding="age" title="Age"/>
	</table>

The result will be a table with two columns and three rows, displaying "Mary | 34", "Jane | 75" and "Jessica | 12" respectively. The title of the first column will be "First Name", and the second column would be "Age".

### Attributes

Here are the attributes usable on the ingredient:

| Attribute | Example                  | Description                          |
|-----------|--------------------------|--------------------------------------|
| title | `title="ID"`          | This is the text label of the link |
| binding  | `binding="id"`          | The databinding of this link |

## text
The text Ingredient allows simple text to be inserted into your page.


Examples

is will just render "Sample text" into your page:

    <text>This is some sample text<text>

This will render the contents of the current data node *email* attribute as your text:

    <text binding="email"></text>

### Attributes

| Attribute | Example                  | Description                                                          |
|-----------|--------------------------|----------------------------------------------------------------------|
| binding   | `binding="data.email"` | Will turn the value of datmaail into the text to render         |
| title     | `title="Email"`        | Will add a title above the text, or as the heading of a table element  |
