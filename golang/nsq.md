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
    SIGHUP 终端连接断开
    SIGTERM kill
    SIGINT ctrl+c
    SIGQUIT ctrl+/
    SIGKILL kill -9
    
2.孤儿进程
若通过kill结束的进程是一个创建过子进程的父进程，则其子进程就会成为孤儿进程（Orphan Process）

**类型推断的消耗**

`type Item struct {
 	d1 int
 	d2 int
 }
 func BenchmarkT1(b *testing.B) {
 	q := make([]*Item, 0)	// 不需要类型推断的 slice
 	for i := 0; i < b.N; i++ {
 		q = append(q, &Item{i, i})
 	}
 	for _, hero := range q {
 		hero.d1++
 	}
 }
 func BenchmarkT2(b *testing.B) {
 	q := make([]interface{}, 0)
 	for i := 0; i < b.N; i++ {
 		q = append(q, &Item{i, i})
 	}
 	for _, hero := range q {
 		hero.(*Item).d1++	// 需要做类型推断
 	}
 }`
 
 测试结果：
 
 `BenchmarkT1-8           10000000               241 ns/op
  BenchmarkT2-8            5000000               332 ns/op
`
