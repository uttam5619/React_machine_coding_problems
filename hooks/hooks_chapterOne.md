
```
function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    setCount(count + 1);
    setCount(count + 2);
    console.log(count);
  }, []);
  return null;
}
```
Output:- 0


```
function App() {
  const [value, setValue] = useState(0);
  useEffect(() => {
    const timer = setTimeout(() => setValue(value + 1), 100);
    return () => clearTimeout(timer);
  }, [value]);
  return <div>{value}</div>;
}
```
What will be the output of the code snippet after 200ms and 500ms.
Output:- 
React 17 - {valur} -> 2 after 200ms, {value} -> 5 after 500ms.
reacct 18+ -> Any infinite loop will result in immediate crash.


```
function App() {
  const [text, setText] = useState('hello');
  useEffect(() => {
    console.log('effect');
    return () => console.log('cleanup');
  }, [text]);
  return <input onChange={e => setText(e.target.value)} />;
}
```
If the user types "world", what order of logs appear?
Output:- 

effect -> on component mount.

cleanup -> just before the first re-render
effect -> on first re-render
cleanup
effect
cleanup
effect
cleanup
effect
cleanup
effect -> on last re-render after writing the letter d


Q. Sequence of logs after mounting, then one click.
```
function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    console.log('Effect A');
    return () => console.log('Cleanup A');
  }, []);
  useEffect(() => {
    console.log('Effect B');
    return () => console.log('Cleanup B');
  }, [count]);
  return <button onClick={() => setCount(count + 1)}>+</button>;
}
```

Output:-

Effect A
Effecct B
Cleanup B
Effect B


Q.What happens on click? (consider React 18 with automatic batching)
```
function App() {
  const [flag, setFlag] = useState(false);
  useEffect(() => {
    if (flag) {
      console.log('Flag is true');
    }
  });
  return <button onClick={() => setFlag(true)}>Set true</button>;
}
```
Output -
Will get immediately crash, because it is triggering an infinite loop.


Q. If the component mounts, unmounts, and mounts again quickly, what happens?
```
function App() {
  const [data, setData] = useState(null);
  useEffect(() => {
    let ignore = false;
    fetch('/api')
      .then(res => res.json())
      .then(result => {
        if (!ignore) setData(result);
      });
    return () => { ignore = true; };
  }, []);
  return <div>{data ? data.name : 'loading'}</div>;
}
```
Output:- 
On Component Mount:- setData() will get called and will mutate the state.
On Component Un-Mount cleaner function will get called
Again On componet-Mount , a new local variable `ignore` will get created and setData() will get called and will mutate the state.


Q.What is the final displayed count?
```
function Child({ onMount }) {
  useEffect(() => {
    onMount();
  }, [onMount]);
  return null;
}

function App() {
  const [count, setCount] = useState(0);
  const handleMount = () => setCount(c => c + 1);
  return (
    <>
      <Child onMount={handleMount} />
      <Child onMount={handleMount} />
      <div>{count}</div>
    </>
  );
}
```
Output:- 



Q. What logs appear, and final state?
```
function App() {
  const [state, setState] = useState({ x: 0 });
  useEffect(() => {
    setState({ x: state.x + 1 });
    setState({ x: state.x + 2 });
  }, []);
  console.log(state.x);
  return null;
}
```

Output:- 
In log we will have 0, ans finally we will have state as 2.


Q. Console output in React 18 Strict Mode
```
function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    console.log('mount');
    setCount(1);
    return () => console.log('unmount');
  }, []);
  useEffect(() => {
    console.log('count updated:', count);
  }, [count]);
  return null;
}
```

Output:-
mount
count updated: 0
unmount
count updated: 1 


Q. If user toggles off after 3 seconds, then toggles on after another 2 seconds, what counter value appears?
```
function useCounter(initial) {
  const [count, setCount] = useState(initial);
  useEffect(() => {
    const id = setInterval(() => setCount(c => c + 1), 1000);
    return () => clearInterval(id);
  }, []);
  return count;
}

function App() {
  const counter = useCounter(0);
  const [show, setShow] = useState(true);
  return (
    <div>
      {show && <p>{counter}</p>}
      <button onClick={() => setShow(!show)}>Toggle</button>
    </div>
  );
}
```
Output: -
After 3s ->3
2s just after first 3 second -> 2.








