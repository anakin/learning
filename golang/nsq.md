**waitgroup 包装**

`type wgwrapper struct {
	sync.WaitGroup
}

func (w *wgwrapper) Wrap(cb func()) {
	w.Add(1)
	go func() {
		cb()
		w.Done()
	}()
}`

**写文件操作**

`func writef(fn string, data []byte) error {
	f, err := os.OpenFile(fn, os.O_WRONLY|os.O_CREATE|os.O_TRUNC, 0600)
	if err != nil {
		return err
	}
	_, err = f.Write(data)
	if err == nil {
		err = f.Sync()
	}
	f.Close()
	return err
}`

**sync.pool的使用**

`
func main() {
     p := &sync.Pool{
         New: func() interface{} {
             return 0
         },
     }
     a := p.Get().(int)
     p.Put(1)
     b := p.Get().(int)
     fmt.Println(a, b)
 }
 `