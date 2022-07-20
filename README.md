# Storybook-Notes

## Config Storybook 

* install && intialize
  * Note this should be installed after you've setup your application e.g. react, angular, vue, etc. 

```command line
npx storybook init
```

* This will generate a `.storybook` folder here you can set several configurations settings in `main.js` && `preview.js`

* You'll also get a `stories` folder in your `src` directory NOTE: this is based on react not sure about other front end frameworks.
  * You don't need any of the default files generated here 
  
## Basic Usage 

* You create entries in storybook with the `.stories` extension, however there are a few different file types that are supported. 
  * The first is Javascript the file name would appear as `fileName.stories.js`
  * The second and arguably better is mdx or markdown extension, this allows you to blend JSX & markdown, the file would appear as `fileName.stories.mdx`
  
### Javascript Stories 

* You'll need to export a default object for this, which will require two properties, `title` & `component` 
  * The `title` property is the the name you'll see in the sidebar of storybook. 
  * The `component` property is the component this story is about. 
  * You can *optionally* add `argTypes` these can be used to shape the way the controls will look or behave. e.g. I want the controls in for the label arg to be a radio with 'red' and 'blue' as options and not an input, I want the name to be 'whatever' and the controls will appear so in the control tab. You can also define an action you want to appear in the `actions` tab as well as shown below. Storybook will automatically map functions named like `onClick` but it is unlikely your function will be named that. 
  
```javascript
import Button from '../components/Button'; 

export default {
  title: 'Button',
  component: Button,
  argTypes: {
    text: { 
      options: [red, blue],
      control: 'radio',
      name: 'whatever'
    },
    handleClick: { action: 'to be printed to the console of storybook when event fires' }
  }
}
```
  
* From here you can define your `stories` each on of these will need to be an exported function, this generally not the recommended way by checkout the below example. You'll need to make sure that you have set your `defaultProps` as well in React for Storybook to know what kind of values these are. 

```javascript
import Button from '../components/Button'; 

export default {
  title: 'Button',
  component: Button
}

export function Red() {
  return <Button backgroundColor="red" text="Red Button" />
}

export function Green() {
  return <Button backgroundColor="green" text="Green Button" />
}
```

* A better way of defining a `story` is by defining a base function allowing storybook the ability to change props on the fly. You'll need to create a reusable template to accomplish this. 

```javascript
function Template(args) {
  return <Button {...args} />
}

export const Red = Template.bind({});
Red.args ={
  backgroundColor: 'red',
  text: 'Red Button'
}

export const Green = Template.bind({});
Green.args ={
  backgroundColor: 'green',
  text: 'Green Button'
}
```

### MDX Stories

* Writing stories in `MDX` is the best way to write stories and is largely similar to writing stories in Javascript. You'll need to first define a `<Meta/>` tag that functions similarly to how the default export did in the JS file. The `<Meta/>` will need to be imported. You can define your `argTypes` here if you'd like or if you want you can have them individualized to the `<Story />` tag discussed later. 

* Additionally `parameters` can be passed to modify the storybook surrounding UI or other elements the example below will allow you to change up the storybook background colors  

```javascript
import { Meta, Story, Canvas };
import Button from '../components/Button'; 

<Meta 
  title="Button" 
  component={Button}
  parameters={{
    backgrounds: {
      values: [
        { name: 'red', value: '#f00' },
        { name: 'green', value: '#0f0' },
        { name: 'blue', value: '#00f' }
      ]
    }
  }}
/>
```

* You'll also want to define a `<Canvas />` this element is what you'll actually display in storybook. A `<Canvas />` has several `<Stories />` inside of it that will make up each story you see in storybook. Here much like JS it is best to define a `Template` and build iterations of that with `args` even if there is only one. The `<Story />` must have a `name` property to render in storybook properly. `<Story />` can also take `args` and `argTypes` as well. If you want your `argTypes` to be global you can define them on the `<Meta />` tag.

```javascript
import { Meta, Story, Canvas };
import Button from '../components/Button'; 

<Meta 
  title="Button" 
  component={Button}
/>

function Template(args) {
  return <Button {...args} />
}

<Canvas>
 <Story 
    name='Button'
    args={{
      backgroundColor: 'red', 
      text: 'Red Button'
    }}
  >
   {Template.bind({})}
 </Story>
</Canvas
```
