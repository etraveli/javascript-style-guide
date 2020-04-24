## Testing

We use `Jest` and `React-testing-library` to test our components and functions.

## Table of Contents

  - [Utils](#utils)
  - [it vs test](#it-vs-test)
  - [Isolate instances](#isolate-instances)
  - [Text](#text)
  - [Interaction with fields](#interaction-with-fields)
  - [Misc](#misc)
   
## Utils

When mounting components you probably need to add some Providers in your render function. There are util functions that can help you, they are located in @eti/test-utils. Here are the functions and how to use them.
```jsx
import { reduxForm } from 'redux-form';
import {
  renderWithReduxProvider,
  renderWithReduxAndTextsProvider,
  renderWithTextsProvider,
  renderWithProviders,
} from '@eti/test-utils';
 
// This functions is the preferred one, it contains all of our newer providers, such as `DirectionsProvider`, `TranslationsProvider` and `SiteContextProvider` but _not_ the old text-provider
const FormField = reduxForm({ form: 'form' })(Component)
const { container } = renderWithReduxProvider(<FormField {...props} />,
{
  // here you can pass in different props
});
 
// This function is for redux - most probably you will need it in a redux-form context.
const state = {
  ifYouNeedAdditionalState: 'pass it here'
};
const FormField = reduxForm({ form: 'form' })(Component);
const { container } = renderWithReduxProvider(<FormField {...props} />, state);
 
// For components that are wrapping text-resolvers.
// Feel free to pass text to the function and test that it is rendered.
// that way we don't need to write a test for our text-resolvers but test what actually is being rendered.
const texts = {
  'App.Label': 'Hello',
};
const { container } = renderWithTextsProvider(<Component {...props} />, texts);
 
// a combination of both above
const { container } = renderWithReduxAndTextsProvider(
  <FormField {...props} />,
  state,
  texts,
);
```
## it vs test

it and test are considered the same. But you should use test when writing your tests in react-testing-library for 2 reasons

 > - jest write test in their documentation on how to write tests
 > - Distinguish which tests are written in enzyme and which are in react-testing-library.

## Isolate instances

As our current test setup is we SHOULD NOT write tests that use the same instance of the component, like this:
```jsx
describe('Component', () => {
  const { container } = render(<Component />);
  test('something', () => {
    expect(container).not.toBe(null);
  });
 
  test('something else', () => {
    expect(container).toBe('something');
  });
});
```

Instead, you SHOULD encapsulate the render like this instead:
```jsx
describe('Component', () => {
  test('something', () => {
    const { container } = render(<Component />);
    expect(container).not.toBe(null);
  });
 
  test('something else', () => {
    const { container } = render(<Component />);
    expect(container).toBe('something');
  });
});
```
But that will slow our test suite down quite a lot, and the test doesn't resemble how the user would use our component - which is one of the things we would like to tests, maybe the most important thing to test really.

Let's take a Counter app for example. For every time a user clicks on the counter button; a new text appears. The user would just click on button all over again to read that beautiful text. A user wouldn't click on the counter button and then reload the page to click on it once again, and then read the new text.

With that in mind, we could write our tests like this instead:
```jsx
describe('Counter', () => {
  test('that text appears every time button is clicked', () => {
    const { getByText, queryByText } = render(<Counter />);
    const button = getByText('button');
    fireEvent.click(button);
 
    expect(getByText('firstText')).toBeInTheDocument();
    // use queryByText when accessing a dom-element that might be null
    expect(queryByText('secondText')).not.toBeInTheDocument();
 
    fireEvent.click(button);
 
    expect(getByText('firstText')).toBeInTheDocument();
    expect(getByText('secondText')).toBeInTheDocument();
  });
});
```
This test resembles more the way a user would use the app.
Which file should I write tests for?



## Text
 You should never write test for our TextResolvers (you should not write them at all, instead use the hook: useTranslation) - that way you can assert the actual translation in the test file that the texts resides.

```jsx
import { useTranslation } from '@eti/providers';
 
const Component = () => {
  const { t } = useTranslation();
  return (
    <h1 data-testid="header">{t('Text.Title')}</h1>
  )
};
```
One way to test this would be like this:
```jsx
test('title is rendered', () => {
  const title = 'title';
  const translations = {
    'Text.Title': title,
  };
  const { getByText } = renderWithProviders(
    <Component />,
  { translations }
  );
 
  expect(getByText(title)).toBeInTheDocument();
  // or with regex
  expect(getByText(/title/i)).toBeInTheDocument();
});
  
// you can also do it with data-testid and toHaveTextContent
test('that title is rendered', () => {
  const title = 'title';
  const translations = {
    'Text.Title': title,
  };
  const { getByTestId } = renderWithProviders(
    <Component />,
  { translations }
  );
  expect(getByTestId('header')).toHaveTextContent(title);
});
```

## Interaction with fields
This is how you can write test for interacting with our input fields

```jsx
import React from 'react';
import { renderWithProviders } from '@eti/test-utils';
import { fireEvent } from '@testing-library/react';
import { reduxForm, reducer } from 'redux-form';
import Comp from '../Comp';
 
test('value and label', () => {
  const label = 'label';
  const placeholder = 'placeholder';
  const form = 'form';
  const FormField = reduxForm({ form })(Comp);
  const { getByText, getByPlaceholderText } = renderWithProviders(
    <FormField form={form} placeholder={placeholder} />,
    {
      translations: { 'Label.TextKey': label },
      reducer, // the key here, is to pass in the reducer from redux-form
    },
  );
  const value = 'value';
  const input = getByPlaceholderText(placeholder);
  fireEvent.change(input, { target: { value } });
  expect(input.value).toBe(value);
  expect(getByText(label)).toBeInTheDocument();
});

```
## Misc
What about a component that looks like this?
```jsx
const Foo = ({ prop1, prop2, prop3, ...rest }) => (
  <section>
    <SomeComponent prop1={prop1} />
    <SomeOtherComponent prop2={prop2} prop3={prop3} />
    <AnotherComponent {...rest} />
  </section>
);
```
Sometimes we have components like this just because it reads better. This component might not even be necessary and the code should reside in the component above. But if it feels like a good idea to have a component like this, you should, of course, introduce it. But does it really need a test file? With enzyme you could prop test this. But that doesn't necessarily mean that it's a good approach. You could argue that the component above should have tests and the 3 components that renders or have some other sort of logic should have tests. If you feel safe with that - don't write test for this Foo component.


How you use async helper functions
```jsx
import Icon from '../icon.svg';

const Foo = () => <Icon />


// test file
import { render, waitFor } from '@testing-library/react';

test('svg is rendered', () => {
  const { getByText } = render(<Foo />);
  await waitFor(() => getByText('icon.svg'));
  // since await function from testing-library throw an error
  // if the element isn't found.
  // which means that we don't really need to actually assert that the 
  // `icon.svg` is in the document
});

```


What assertions goes in to one test block?
If the props stay the same - assert everything that you can inside one test block.
```jsx
const FooBar = ({ foo, bar, label, text }) => (
  <div>
    <span>{label}</span>
    {foo ? <p>{bar}</p> : <p>{text}</p>}
  </div>
);

// FooBar.test.jsx
test('bar is rendered when foo is true', () => {
    const text = 'text';
    const bar = 'bar';
    const label = 'label';
    const { getByText, queryByText } = render(
      <FooBar foo text={text} bar={bar} label={label} />
    );   
    expect(getByText(bar)).toBeInTheDocument();
    expect(getByText(label)).toBeInTheDocument();
    expect(queryByText(text)).not.toBeInTheDocument();   
});
 
 
test('text is rendered when foo is false', () => {
    const text = 'text';
    const bar = 'bar';
    const label = 'label';
    const { getByText, queryByText } = render(
      <FooBar foo={false} text={text} bar={bar} label={label} />
    );
    expect(queryByText(bar)).not.toBeInTheDocument();  
    expect(getByText(text)).toBeInTheDocument();   
});
```


### **Don't force tests, write tests that feel useful rather than test every code line**
