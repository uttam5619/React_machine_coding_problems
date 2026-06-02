Q Click + three times rapidly, then after 2 seconds what logs?
```
function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    setTimeout(() => {
      console.log(count);
    }, 2000);
  }, []);
  return <button onClick={() => setCount(count + 1)}>+</button>;
}
```
Output:-  0


Q. Click once. Console output and final count?
```
function App() {
  const [count, setCount] = useState(0);
  const onClick = () => {
    setCount(count + 1);
    setCount(count + 2);
    console.log(count);
  };
  return <button onClick={onClick}>+</button>;
}
```
Output:-  0


`to be reviewed`
Q. Click once, anad what will get logged?
```
function App() {
  const [count, setCount] = useState(0);
  const onClick = async () => {
    await Promise.resolve();
    setCount(count + 1);
    setCount(count + 2);
    console.log(count);
  };
  return <button onClick={onClick}>+</button>;
}
```


Q. Final displayed count?
```
function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    setCount(count + 5);
    setCount(count => count + 10);
  }, []);
  return <div>{count}</div>;
}
```
Output: - 15.
setCount(count+5) will get evaluated as setCount(0+5) -> this will set tha new state value to 5. Now when 
setCount(count => count + 10 ) will get called, count will read the previous state value that is 5 and again add 10 to it.


Q. Click once. Order of logs?
```
function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    console.log('effect', count);
    return () => console.log('cleanup', count);
  }, [count]);
  return <button onClick={() => setCount(1)}>set to 1</button>;
}
```
Output:- 
effect 0
cleanup 0
effect 1


Q. Console output in development?
```
function App() {
  const [x, setX] = useState(0);
  useEffect(() => {
    console.log('mount');
    setX(10);
    console.log(x)
    return () => console.log('unmount');
  }, []);
  return null;
}
```
Output:- 
mount
0
unmount
mount
10


Q. Toggle once. Logs?
```
function App() {
  const [show, setShow] = useState(true);
  return (
    <div>
      <button onClick={() => setShow(!show)}>toggle</button>
      {show && <Child key={show ? 'a' : 'b'} />}
    </div>
  );
}
function Child() {
  useEffect(() => {
    console.log('mount');
    return () => console.log('unmount');
  }, []);
  return <div>child</div>;
}
```
Output:-
Initally -> mount.
After toggle -> unmount.


Q. Logs after mounting then one click?
```
function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    console.log('A');
    return () => console.log('B');
  }, [count]);
  useEffect(() => {
    console.log('C');
    return () => console.log('D');
  }, []);
  return <button onClick={() => setCount(1)}>click</button>;
}
```
Output:- 
Initially -> A C
After first click ->B A


Q. What happens?
```
function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const timer = setTimeout(() => setCount(count + 1), 100);
    return () => clearTimeout(timer);
  });
  return <div>{count}</div>;
}
```
Output:- 0


Q. What happens?
```
function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const timer = setInterval(() => setCount(count + 1), 100);
    return () => clearInterval(timer);
  });
  return <div>{count}</div>;
}
```
Output:- 
Before React 18 -> 1,2,3,4,5..... 
After react 18 -> Immediate crash.


Q. What happens ?
```
function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const timer = setInterval(() => setCount(count + 1), 100);
    return () => clearInterval(timer);
  },[count]);
  return <div>{count}</div>;
}
```
Output:-
Before React 18 -> 1,2,3,4,5...
After React 18 -> Immediate crash.


Q. Click once. Console and final values?
```
function App() {
  const [a, setA] = useState(0);
  const [b, setB] = useState(0);
  const click = () => {
    Promise.resolve().then(() => setA(1));
    setB(2);
    console.log(a, b);
  };
  return <button onClick={click}>click</button>;
}
```
Output:-
0 0


Q. Console and final count?
```
function App() {
  const [count, setCount] = useState(0);
  const onClick = () => {
    setCount(c => c + 1);
    setCount(c => c + 1);
    console.log(count);
  };
  return <button onClick={onClick}>+</button>;
}
```
Output:-
0
Final Value of state -> 2


Q. Console and final count?
```
function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    setTimeout(() => {
      setCount(c => c + 1);
      setCount(c => c + 1);
      console.log(count);
    }, 100);
  }, []);
  return null;
}
```
Output:- 
2
Value of state after setTimeOut -> 2.


Q. After one click what will be the value of count ?
```
function App() {
  const [count, setCount] = useState(0);
  const increment = () => {
    setCount(count + 1);
    setCount(count + 1);
    setCount(count + 1);
  };
  return <button onClick={increment}>+</button>;
}
```
Output:-
1


Q. After one click what will be the value of count ?
```
function App() {
  const [count, setCount] = useState(0);
  const increment = () => {
    setCount(count + 1);
    setCount(count => count + 1);
    setCount(count + 1);
  };
  return <button onClick={increment}>+</button>;
}
```
Output:-
1


Q. Quick unmount + remount. What happens?
```
function App() {
  const [user, setUser] = useState(null);
  useEffect(() => {
    let cancelled = false;
    fetch('/user')
      .then(res => res.json())
      .then(data => {
        if (!cancelled) setUser(data);
      });
    return () => { cancelled = true; };
  }, []);
  return <div>{user?.name}</div>;
}
```
On unmount + remount,
At this point, there are two separate network requests flying through the internet, and two separate closures waiting for them.
When Fetch #1 finishes:The .then() block for the first fetch triggers. Because it forms a closure over the variables of Mount #1, it checks that specific cancelled variable.
- It sees if (!cancelled) $\rightarrow$ since cancelled is true, the condition fails.
- Result: setUser(data) is not called. The first network response is safely ignored, preventing a state update on an unmounted component or stale data.
When Fetch #2 finishes:The .then() block for the second fetch triggers. It looks at the closure for Mount #2.
- It sees if (!cancelled) $\rightarrow$ since Mount #2 was never unmounted, its cancelled variable is still false.
- Result: setUser(data) executes successfully. The screen updates with the user's name.


Q. 
```
function App() {
  const [query, setQuery] = useState('react');
  const [results, setResults] = useState([]);
  useEffect(() => {
    let ignore = false;
    fetch(`/search?q=${query}`)
      .then(res => res.json())
      .then(data => {
        if (!ignore) setResults(data);
      });
    return () => { ignore = true; };
  }, [query]);
  return <input onChange={e => setQuery(e.target.value)} />;
}
```

On mount the fetch request will get executed and it will setResults() after promise getting resolved. Since the dependency array is carring the query , and the state "query" is not getting impacted , so nither the cleaner will get called nor any re-render will take place.

`to be viewed`
Q. Unmount during fetch → console output?
```
function App() {
  const [data, setData] = useState(null);
  useEffect(() => {
    const controller = new AbortController();
    fetch('/data', { signal: controller.signal })
      .then(res => res.json())
      .then(setData)
      .catch(err => console.log(err.name));
    return () => controller.abort();
  }, []);
  return null;
}
```


Q. What will be the output in the strict mode.
```
let global = 0;
function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    global++;
    setCount(global);
  }, []);
  return <div>{count}</div>;
}
```
Output:-
2
It is so , because in strict mode the component will get rendered twice.


Q. Click once. Console?
```
function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    setCount(100);
    console.log(count);
  }, [count]);
  return <button onClick={() => setCount(1)}>click</button>;
}
```
Output:- 
100
Even after first click the console will print 100.


`to be reviewed`
Q. What does user see initially?
```
function App() {
  const [width, setWidth] = useState(0);
  useLayoutEffect(() => {
    const el = document.getElementById('box');
    setWidth(el.getBoundingClientRect().width);
  }, []);
  return <div id="box" style={{ width: '50%' }}>{width}</div>;
}
```

Q. Click three times. Console logs?
```
function App() {
  const ref = useRef(0);
  const [ , forceUpdate] = useState(0);
  const click = () => {
    ref.current++;
    forceUpdate(i => i + 1);
    console.log(ref.current);
  };
  return <button onClick={click}>+</button>;
}
```
Output:-
1
2
3


Q. Click + three times. Console logs over time?
```
function App() {
  const [count, setCount] = useState(0);
  const ref = useRef(count);
  useEffect(() => {
    ref.current = count;
  }, [count]);
  useEffect(() => {
    const id = setInterval(() => {
      console.log(ref.current);
    }, 1000);
    return () => clearInterval(id);
  }, []);
  return <button onClick={() => setCount(c => c + 1)}>+</button>;
}
```
Initially -> 0. (on each interval of 1 sec, before clicking three times)
After 3 clicks -> 3 (on each interval of 1 sec) 


Q. Click once. Console and button text sequence?
```
function App() {
  const [isPending, startTransition] = useTransition();
  const [count, setCount] = useState(0);
  const click = () => {
    startTransition(() => {
      setCount(100);
    });
    console.log(count);
  };
  return <button onClick={click}>{isPending ? 'loading' : count}</button>;
}
```
Output:-
After one click.
console -> 0
100


Q. Type 'a', then 'b'. Console logs?
```
function App() {
  const [value, setValue] = useState('');
  const onChange = e => setValue(e.target.value);
  useEffect(() => {
    console.log('Effect:', value);
  });
  return <input onChange={onChange} />;
}
```
Output
a
ab


Q. Click multiple times. Console?
```
function App() {
  const [count, setCount] = useState(0);
  const badSetCount = () => {
    setCount(count);
    console.log(count);
  };
  return <button onClick={badSetCount}>click</button>;
}
```
Output:-
0


Q. Console output over 3 seconds?
```
function useInterval(callback, delay) {
  useEffect(() => {
    const id = setInterval(callback, delay);
    return () => clearInterval(id);
  }, [callback, delay]);
}
function App() {
  const [count, setCount] = useState(0);
  useInterval(() => {
    setCount(count + 1);
    console.log(count);
  }, 1000);
  return <div>{count}</div>;
}
```
Output:-






