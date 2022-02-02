```tsx
const [item, setItem] = useState(0);
const [queue, setQueue] = useState([]);
const queueRef = useRef(queue);

useEffect(() => {
  setQueue(queueRef.current.concat(item));
}, [item]);

useEffect(() => {
  console.log("The queue was updated!");
}, [queue]);

console.log(queue); // [0, 1, 2, 3, ...]
```
