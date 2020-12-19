  * [Introduction](#introduction)    
  * [Cache](#cache)    
            


#### Introduction
This is an example of a section, referred in the index above. Use anchors to facilitate navigation.

#### Cache

Cache is where things happens. This is another section to help show how they stack.

![example image](images/example_image.png)

There is an image above. Check name and path if it is not showing right.

###### Source code example

This is a source code example. Don't forget to add it to the src folder so people won't have to copy & paste.

```
/*
IncrementCounter increments <<name>> counter by adding <<item&gt>> to it.
The naive implementation locks(), get, increment and set
The counter and its lock are automatically created if it is empty.
*/
func (hc *HLLCounters) IncrementCounter(key []byte, item []byte) error {
	if hc.hllrwlocks[string(key)] == nil {
		hc.hllrwlocks[string(key)] = new(sync.RWMutex)
	}

	localMutex := hc.hllrwlocks[string(key)]
	localMutex.Lock()
	defer localMutex.Unlock()

	cc, _ := hc.datastorage.Get([]byte(key))

	hll := hyperloglog.New16()
	if cc != nil {
		if err := hll.UnmarshalBinary(cc); err != nil {
			return err
		}
	} else {
		hc.stats.ActiveCounters++
	}

	hll.Insert(item)
	var bd []byte
	var err error

	if bd, err = hll.MarshalBinary(); err != nil {
		return err
	}

	if err = hc.datastorage.Add([]byte(key), bd); err != nil {
		return err
	}
	return nil
}
```

