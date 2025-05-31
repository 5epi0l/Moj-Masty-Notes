
One of the first things to do when given access to an internal network, is to look for active hosts that we can target. We can do this in multiple ways. Here is a way of discovering hosts using fping,


```bash
fping -agq <subnet>
```


- **-a** : show systems that are alive
- **-g** : generates a target list from a supplied IP netmask
- **-q** : quiet mode, doesn't show per-probe results
- 