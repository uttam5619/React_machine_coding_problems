Q. What will be the output when query string will change
```
function SearchResults({ query }) {
  const [results, setResults] = useState([]);

  useEffect(() => {
    let ignore = false;
    fetch(`/api/search?q=${query}`)
      .then(res => res.json())
      .then(data => {
        if (!ignore) setResults(data);
      });
    return () => { ignore = true; };
  }, [query]);

  return <div>{results.length} results</div>;
}
```

Output
when query string will change it will call the effect handler for each individual query.
Here cleaner functions has no role in optimization, it will just change the value of the stale ignore variable in closure.


Q. Predict the output just after 2s and after 3s
```
function Counter() {
  const [num, setNum] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setNum(n => n + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);

  useEffect(() => {
    setTimeout(() => {
      setNum(100);
    }, 2500);
  }, []);

  return <div>{num}</div>;
}
```
Output:-
After 2 s -> 2
After 3 s -> 101


Q. predict the output , just after 2s and 3s.
```
function Counter() {
  const [num, setNum] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setNum(num + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);

  useEffect(() => {
    setTimeout(() => {
      setNum(100);
    }, 2500);
  }, []);

  return <div>{num}</div>;
}
```
Output:-
After 2sec -> 1
After 3ssec -> 1 


Q. Predict the output jsut after 2 sec and 3 sec
```
function Counter() {
  const [num, setNum] = useState(0);

  useEffect(() => {
    const id = setInterval(() => {
      setNum(num + 1);
    }, 1000);
    return () => clearInterval(id);
  }, []);

  useEffect(() => {
    setTimeout(() => {
      setNum(100);
    }, 2500);
  }, []);

  return <div>{num}</div>;
}
```