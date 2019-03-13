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
 1.数量不可控
 2.生命周期在两次gc之间
 
**信号处理**

`c := make(chan os.Signal)
 	signal.Notify(c)
 	s := <-c
 	fmt.Println("receive:",s)`
1.常见信号：
    SIGUP 终端连接断开
    SIGTERM kill
    SIGINT ctrl+c
    SIGQUIT ctrl+/
    SIGKILL kill -9
    
2.孤儿进程
若通过kill结束的进程是一个创建过子进程的父进程，则其子进程就会成为孤儿进程（Orphan Process）
