### React
In other words, React apps are made out of components, but what makes React special isn’t components themselves. What makes React special is the ways in which components interact.

### component/component class/component instance
On line 4, by subclassing `React.Component`, you create a new component class. **This is not a component!** A component class is more like a factory that produces components. When you start making components, each one will come from a component class.
```
class MyComponentClass extends React.Component {
```
#### a component class
```
class MyComponentClass extends React.Component {}
```
#### a component instance/a component
```
<MyComponentClass />
```

