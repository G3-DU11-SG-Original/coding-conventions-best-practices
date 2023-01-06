
# React code conventions and best practices

React code conventions and best practices


## Authors

- [@DevLinh](https://www.github.com/DevLinh)



## Import order
Add some import order rules to your eslint config. This will ensure that the imports are always in the same order and are grouped by predefined rules.
```javascript
{
  'import/order': [
    2,
    {
      'newlines-between': 'always',
      groups: [
        'builtin',
        'external',
        'internal',
        'parent',
        'sibling',
        'index',
        'unknown',
        'object',
        'type',
      ],
      alphabetize: {
        order: 'asc',
        caseInsensitive: true,
      },
      pathGroups: [
        {
          pattern: 'react*',
          group: 'external',
          position: 'before',
        },
      ],
    },
  ],
}
```

## Naming conventions
### Use PascalCase in components, interfaces, or type aliases
```javascript
// React component
const BannersEditForm = () => {
  ...
}

// Typescript interface
interface TodoItem {
  id: number;
  name: string;
  value: string;
}

// Typescript type alias
type TodoList = TodoItem[];
```

### Use camelCase for JavaScript data types like variables, arrays, objects, functions, etc.
```javascript
const getLastDigit = () => { ... }

const userTypes = [ ... ]
```

### Use camelCase for folder and non-component file names and PascalCase for component file names
```javascript
src/utils/form.ts
src/hooks/useForm.ts
src/components/banners/edit/Form.tsx
```

## Avoid default export
Default exports don’t associate any name with the item being exported, meaning that any name can be applied during import. This may give you a flexibility, but if multiple developers imports the same module with different names or even non descriptive name, we are screwed.

Named exports are explicit, forcing the consumer to import with the names the original author intended and removing any ambiguity.
```javascript
// 1. Imports - Prefer destructuring imports to minimize writen code
import React, { PropsWithChildren, useState, useEffect } from "react";

// 2. Types
type ComponentProps = {
  someProperty: string;
};

// 3. Styles - with @mui use styled API or sx prop of the component
const Wrapper = styled("div")(({ theme }) => ({
  color: theme.palette.white
}));

// 4. Additional variables
const SOME_CONSTANT = "something";

// 5. Component
function Component({ someProperty }: PropsWithChildren<ComponentProps>) {
  // 5.1 Definitions
  const [state, setState] = useState(true);
  const { something } = useSomething();

  // 5.2 Functions
  function handleToggleState() {
    setState(!state);
  }

  // 5.3 Effects
  // ❌
  React.useEffect(() => {
    // ...
  }, []);

  // ✅
  useEffect(() => {
    // ...
  }, []);

  // 5.5 Additional destructures
  const { property } = something;

  return (
    <div>
      {/* Separate elements if not closed on the same line to make the code clearer */}
      {/* ❌ */}
      <div>
        <div>
          <p>Lorem ipsum</p>
          <p>Pellentesque arcu</p>
        </div>
        <p>Lorem ipsum</p>
        <p>Pellentesque arcu</p>
      </div>
      <div>
        <p>
          Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Pellentesque
          arcu. Et harum quidem rerum facilis est et expedita distinctio.
        </p>
        <p>Pellentesque arcu</p>
        <p>
          Lorem ipsum dolor sit amet, consectetuer adipiscing elit. Pellentesque
          arcu. Et harum quidem rerum facilis est et expedita distinctio.
        </p>
      </div>

      {/* ✅ */}
      <Wrapper>
        <div>
          <p>Lorem ipsum</p>
          <p>Pellentesque arcu</p>
        </div>

        <p>Lorem ipsum</p>
        <p>Pellentesque arcu</p>
      </Wrapper>

      <div>
        <div>
          <p>
            Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
            Pellentesque arcu. Et harum quidem rerum facilis est et expedita
            distinctio.
          </p>

          <p>Pellentesque arcu</p>

          <p>
            Lorem ipsum dolor sit amet, consectetuer adipiscing elit.
            Pellentesque arcu. Et harum quidem rerum facilis est et expedita
            distinctio.
          </p>
        </div>
      </div>
    </div>
  );
}

// 6. Exports
export { Component };
export type { ComponentProps };
```

## Use PropsWithChildren

```javascript
import React, { PropsWithChildren } from "react";

type ComponentProps = {
  someProperty: string;
};

// ✅
function Component({ someProperty, children }: PropsWithChildren<ComponentProps>) {
  // ...
}
```

## Separate function from the JSX if it takes more than 1 line

```javascript
// ❌
<button
  onClick={() => {
    setState(!state);
    resetForm();
    reloadData();
  }}
/>

// ✅
<button onClick={() => setState(!state)} />

// ✅
const handleButtonClick = () => {
  setState(!state);
  resetForm();
  reloadData();
}

<button onClick={handleButtonClick} />
```
## Avoid using indexes as key props

```javascript
// ❌
const List = () => {
  const list = ["item1", "item2", "item3"];

  return (
    <ul>
      {list.map((value, index) => {
        return <li key={index}>{value}</li>;
      })}
    </ul>
  );
};

// ✅
const List = () => {
  const list = [
    { id: "111", value: "item1" },
    { id: "222", value: "item2" },
    { id: "333", value: "item3" }
  ];

  return (
    <ul>
      {list.map((item) => {
        return <li key={item.id}>{item.value}</li>;
      })}
    </ul>
  );
};
```

## Use fragments

```javascript
// ❌
const ActionButtons = ({ text1, text2 }) => {
  return (
    <div>
      <button>{text1}</button>
      <button>{text2}</button>
    </div>
  );
};

// ✅
const Button = ({ text1, text2 }) => {
  return (
    <>
      <button>{text1}</button>
      <button>{text2}</button>
    </>
  );
};
```

## Prefer destructuring properties
Prefer destructuring properties so it is clear what properties are used in the component.

```javascript
// ❌
const Button = (props) => {
  return <button>{props.text}</button>;
};

// ✅
const Button = (props) => {
  const { text } = props;

  return <button>{text}</button>;
};

// ✅
const Button = ({ text }) => {
  return <button>{text}</button>;
};
```

## Separation of concerns

Separating the business logic from the presentation (rendering) makes the components code more readable. Most of the time this is applicable for the page/screen/container components where you are about to use multiple hooks or useEffects. Then the final code starts to be huge enough to be unreadable.

## Custom hooks
To separate the responsibilities first of all you should create custom hooks instead of just putting a useEffect or multiple useStates directly to your component.
```javascript

// ❌
const ScreenDimensions = () => {
  const [windowSize, setWindowSize] = useState({
    width: undefined,
    height: undefined
  });

  useEffect(() => {
    function handleResize() {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    }

    window.addEventListener("resize", handleResize);
    handleResize();
  
    return () => window.removeEventListener("resize", handleResize);
  }, []);

  return (
    <>
      <p>Current screen width: {windowSize.width}</p>
      <p>Current screen height: {windowSize.height}</p>
    </>
  );
};

// ✅
const useWindowSize = () => {
  const [windowSize, setWindowSize] = useState({
    width: undefined,
    height: undefined
  });

  useEffect(() => {
    function handleResize() {
      setWindowSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    }

    window.addEventListener("resize", handleResize);
    handleResize();

    return () => window.removeEventListener("resize", handleResize);
  }, []);

  return windowSize;
};

const ScreenDimensions = () => {
  const windowSize = useWindowSize();

  return (
    <>
      <p>Current screen width: {windowSize.width}</p>
      <p>Current screen height: {windowSize.height}</p>
    </>
  );
};
```

## Avoid huge components
Whenever it is possible, split your component into smaller chunks. Often applicable when you are using conditional rendering or defining the columns for a data grid, etc. Also applicable when a lot of custom hooks are used — check the section above.

```javascript

// ❌
const SomeSection = ({ isEditable, value }) => {
  if (isEditable) {
    return (
      <Section>
        <Title>Edit this content</Title>
        <Content>{value}</Content>
        <Button>Clear content</Button>
      </Section>
    );
  }

  return (
    <Section>
      <Title>Read this content</Title>
      <Content>{value}</Content>
    </Section>
  );
};

// ✅
const EditableSection = ({ value }) => {
  return (
    <Section>
      <Title>Edit this content</Title>
      <Content>{value}</Content>
      <Button>Clear content</Button>
    </Section>
  );
};

const DetailSection = ({ value }) => {
  return (
    <Section>
      <Title>Read this content</Title>
      <Content>{value}</Content>
    </Section>
  );
};

const SomeSection = ({ isEditable, value }) => {
  return isEditable ? (
    <EditableSection value={value} />
  ) : (
    <DetailSection value={value} />
  );
};
```

## Group the state whenever possible

```
// ❌
const [username, setUsername] = useState('')
const [password, setPassword] = useState('')

// ✅
const [user, setUser] = useState({})
```

## Use shorthand for boolean props
```
// ❌
<Form hasPadding={true} withError={true} />

// ✅
<Form hasPadding withError />
```

## Avoid curly braces for string props
```
// ❌
<Title variant={"h1"} value={"Home page"} />

// ✅
<Title variant="h1" value="Home page" />
```

## Avoid using inline styles
Ideally, use some third party for handling styles like CSS modules, JSS, styled-components, etc. or create a custom hook.
```
// ❌
const Title = (props) => {
  return (
    <h1 style={{ fontWeight: 600, fontSize: '24px' }} {...props}>
      {children}
    </h1>
  );
};

// ✅
const useStyles = (props) => {
  return useMemo(
    () => ({
      header: { fontWeight: props.isBold ? 700 : 400, fontSize: "24px" }
    }),
    [props]
  );
};

const Title = (props) => {
  const styles = useStyles(props);

  return (
    <h1 style={styles.header} {...props}>
      {children}
    </h1>
  );
};
```

## Prefer conditional rendering with ternary operator
```
const { role } = user;

// ❌
if (role === ADMIN) {
  return <AdminUser />;
} else {
  return <NormalUser />;
}

// ✅
return role === ADMIN ? <AdminUser /> : <NormalUser />;
```

## Use constants or enums for string values
```
// ❌
if (role === 'admin') {
  return <AdminUser />;
}

// ✅
enum Roles {
  admin = 'admin',
  basic = 'basic',
}

if (role === Roles.admin) {
  return <AdminUser />;
}
```

## Use type aliases
```typescript
export type TodoId = number;
export type UserId = number;

export interface Todo {
  id: TodoId;
  name: string;
  completed: boolean;
  userId: UserId;
}

export type TodoList = Todo[];
```

## Don’t use third-party libraries directly
Instead of importing the third-party libraries directly, re-export them in one centralised place.

src/lib/store.ts
```
export { useDispatch, useSelector } from 'react-redux';
```
src/lib/query.ts
```
export { useQuery, useMutation, useQueryClient } from 'react-query';
```

## Rely on abstraction instead of the implementation

```
// ❌ directly using momemt
import moment from 'moment';

const updateProduct = (product) => {
  const payload = {
    ...product,
    // ❌ we are bound to the moment interface implementation
    updatedAt: moment().toDate(),
  };

  return await fetch(`/product/${product.id}`, {
    method: 'PUT',
    body: JSON.stringify(payload),
  });
};

// ✅ creating the abstraction, a.k.a. helper function which wraps the functionality

// utils/createDate.ts
import moment from 'moment';

export const createDate = (): Date => moment().toDate();

// updateProduct.ts
import { createDate } from './utils/createDate';

const updateProduct = (product) => {
  const payload = {
    ...product,
    // ✅ using the abstracted helper function
    updatedAt: createDate(),
  };

  return await fetch(`/product/${product.id}`, {
    method: 'PUT',
    body: JSON.stringify(payload),
  });
};
```

## Prefer declarative programming
```
// ❌ imperative: dealing with internals of array iteration
const arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
let sum = 0;

for (let i = 0; i < arr.length; i++) {
  sum += arr[i];
}

// ✅ declarative: we don't deal with internals of iteration
const arr = [1, 2, 3, 4, 5, 6, 7, 8, 9];
const sum = arr.reduce((acc, v) => acc + v, 0);
```

## Use descriptive variable names
```
// ❌ Avoid single letter names
const n = 'Max';
// ✅
const name = 'Max';

// ❌ Avoid abbreviations
const sof = 'Sunday';
// ✅
const startOfWeek = 'Sunday';

// ❌ Avoid meaningless names
const foo = false;
// ✅
const appInit = false;
```

## Avoid long list of function arguments
```
// ❌
function createPerson(firstName, lastName, height, weight, gender) {
  // ...
}

// ✅
function createPerson({ firstName, lastName, height, weight, gender }) {
  // ...
}

// ✅
function createPerson(person) {
  const { firstName, lastName, height, weight, gender } = person;
  // ...
}
```

## Use object destructuring
```
// ❌
return (
  <>
    <div> {user.name} </div>
    <div> {user.age} </div>
    <div> {user.profession} </div>
  </>  
)

// ✅
const { name, age, profession } = user;

return (
  <>
    <div> {name} </div>
    <div> {age} </div>
    <div> {profession} </div>
  </>  
)
```

## Prefer using template literals
```
// ❌
const userName = user.firstName + " " + user.lastName;

// ✅
const userDetails = `${user.firstName} ${user.lastName}`;
```

## Use implicit return in small functions
```
// ❌
const add = (a, b) => {
  return a + b;
}

// ✅
const add = (a, b) => a + b;
```

