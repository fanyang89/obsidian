基准
![|300](Pasted%20image%2020240203123650.png)

```go
func (t *Gosmb2Test) TestGosmb2Copy1() {
	t.Require().NoError(WithFileRO(testFile1, func(f *os.File, size int64) error {
		dst, err := t.share.OpenFile("test-file1", os.O_CREATE|os.O_TRUNC|os.O_RDWR, 0644)
		if err != nil {
			log.Fatal().Err(err).Msg("open dst failed")
		}
		defer func() { _ = dst.Close() }()

		bar := progressbar.DefaultBytes(size)
		defer func() { _ = bar.Finish() }()

		_, err = io.Copy(io.MultiWriter(dst, bar), f)
		return err
	}))
}

=== RUN   TestExampleTestSuite/TestGosmb2Copy1
 100% |████████████████████████████████████████| (524/524 MB, 28 MB/s)
--- PASS: TestExampleTestSuite (19.52s)
    --- PASS: TestExampleTestSuite/TestGosmb2Copy1 (19.52s)
```

```go
func (t *Gosmb2Test) TestGosmb2Copy2() {
	t.Require().NoError(WithFileRO(testFile1, func(f *os.File, size int64) error {
		dst, err := t.share.OpenFile("test-file1", os.O_CREATE|os.O_TRUNC|os.O_RDWR, 0644)
		if err != nil {
			log.Fatal().Err(err).Msg("open dst failed")
		}
		defer func() { _ = dst.Close() }()

		bar := progressbar.DefaultBytes(size)
		defer func() { _ = bar.Finish() }()

		buf := make([]byte, 81920)
		_, err = io.CopyBuffer(io.MultiWriter(dst, bar), f, buf)
		return err
	}))
}

=== RUN   TestExampleTestSuite/TestGosmb2Copy2
 100% |████████████████████████████████████████| (524/524 MB, 40 MB/s)
--- PASS: TestExampleTestSuite (13.28s)
    --- PASS: TestExampleTestSuite/TestGosmb2Copy2 (13.28s)
```

```
=== RUN   TestGoSmb2
=== RUN   TestGoSmb2/TestGosmb2Copy1
  97% |█████████████████████
 100% |████████████████████████████████████████| (524/524 MB, 20 MB/s)
=== RUN   TestGoSmb2/TestGosmb2Copy2
 100% |████████████████████████████████████████| (524/524 MB, 29 MB/s)
=== RUN   TestGoSmb2/TestGosmb2Copy3
   0% |                                                 | ( 0 B/524 MB) [0s:0s]2024-02-03T14:12:00+08:00 INF smb_test.go:139 > source file size=524288000
2024-02-03T14:12:00+08:00 INF smb_test.go:157 > Worker start len=65536000 offset=0 worker=0
2024-02-03T14:12:00+08:00 INF smb_test.go:157 > Worker start len=65536000 offset=65536000 worker=1
2024-02-03T14:12:00+08:00 INF smb_test.go:157 > Worker start len=65536000 offset=131072000 worker=2
2024-02-03T14:12:00+08:00 INF smb_test.go:157 > Worker start len=65536000 offset=196608000 worker=3
2024-02-03T14:12:00+08:00 INF smb_test.go:157 > Worker start len=65536000 offset=262144000 worker=4
2024-02-03T14:12:00+08:00 INF smb_test.go:157 > Worker start len=65536000 offset=327680000 worker=5
2024-02-03T14:12:00+08:00 INF smb_test.go:157 > Worker start len=65536000 offset=393216000 worker=6
2024-02-03T14:12:00+08:00 INF smb_test.go:157 > Worker start len=65536000 offset=458752000 worker=7
 100% |████████████████████████████████████████| (524/524 MB, 122 MB/s)
--- PASS: TestGoSmb2 (59.15s)
    --- PASS: TestGoSmb2/TestGosmb2Copy1 (26.08s)
    --- PASS: TestGoSmb2/TestGosmb2Copy2 (18.02s)
    --- PASS: TestGoSmb2/TestGosmb2Copy3 (15.05s)
PASS
```
