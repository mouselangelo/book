## Testing stateful components using Enzyme

We talked about testing presentational components using our beloved feature called Snaphot testing in Jest. But It just tests the UI of the component(just the render method of your component).

_What if your component contains some class methods? What if your component contains state?_

Thats where we use __enzyme__.

### What's enzyme?

Enzyme is a JavaScript Testing utility for React. You will mostly be using shallow utility from enzyme. Shallow utility helps us rendering a component and allows us accessing the class methods/state of the component.

### Integrating Enzyme in your current Jest Framework

The default react-native boilerplate comes with Jest. Integrating enzyme with Jest is just a two step process.

- Install enzyme and jest-enzyme `yarn add enzyme jest-enzyme --dev`

- Add one line in package.json inside jest config:
    `"setupTestFrameworkScriptFile": "./node_modules/jest-enzyme/lib/index.js",`

Thats it. You can start using Enzyme utilities now.

### Using Shallow renderer from enzyme:

- First we need to shallow render our component.

```js
import {shallow} from 'enzyme';
describe('SomeComponent component', () => {
  it('Shallow rendering', () => {
    const wrapper = shallow(<SomeComponent {..props}/>);
  });
});
```

Now Our component is rendered and we can access props/state/methods using `wrapper`. Here is how you access them:


```js
import {shallow} from 'enzyme';
describe('SomeComponent component', () => {
  it('Shallow rendering', () => {
    const wrapper = shallow(<SomeComponent someProp={1}/>);
    const componentInstance = wrapper.instance();
    //Accessing react lifecyle methods
    componentInstance.componentDidMount();
    componentInstance.componentWillMount();
    //Accessing component state
    expect(wrapper.state('someStateKey')).toBe(true);
    //Accessing component props
    expect(wrapper.props.someProp).toEqual(1);
    //Accessing class methods
    expect(componentInstance.counter(1)).toEqual(2);
  });
});
```

As you saw, you can access everything a component possess using shallow utitity. You can also have a look at the example test case in our boilerplate code [here]().


### Example

Lets take an example of a component with state and class method. We will write test case for the methods including snapshot test. The example includes testing class methods, state and props.

```js
import React from 'react';
import renderer from 'react-test-renderer';
import {shallow} from 'enzyme';
import Counter from '../Counter.component';

describe('Counter component', () => {
  it('Counter: renders correctly', () => {
    const tree = renderer.create(<Counter />).toJSON();
    expect(tree).toMatchSnapshot();
  });
  it('componentWillMount: should set the passed initialCountValue to state', () => {
    const wrapper = shallow(<Counter initialCountValue={2}/>);
    expect(wrapper.instance().state.count).toBe(2);
  });
  it('incrementCounter: should increment state.count by 1', () => {
    const wrapper = shallow(<Counter initialCountValue={0}/>);
    const instance = wrapper.instance();
    expect(instance.state.count).toBe(0);
    instance.incrementCounter();
    expect(instance.state.count).toBe(1);
  });
  it('decrementCounter: should decrement state.count by 1', () => {
    const wrapper = shallow(<Counter initialCountValue={1}/>);
    const instance = wrapper.instance();
    expect(instance.state.count).toBe(1);
    instance.decrementCounter();
    expect(instance.state.count).toBe(0);
  });
  it('should call props on increment/decrement', () => {
    const incrementSpy = jest.fn();
    const decrementSpy = jest.fn();
    const wrapper = shallow(<Counter initialCountValue={1} onIncrement={incrementSpy} onDecrement={decrementSpy}/>);
    const instance = wrapper.instance();
    instance.incrementCounter();
    expect(incrementSpy).toBeCalledWith(2);
    instance.decrementCounter();
    expect(decrementSpy).toBeCalledWith(1);
  });
});
```


_Note: You can find this example working live [here](https://github.com/master-atul/react-native-plus-plus-example-demo/tree/master/app/components/Counter)._