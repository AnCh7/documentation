## Table Testing in Go

> References:
> https://www.toptal.com/go/your-introductory-course-to-testing-with-go
> https://rms1000watt.github.io/post/testing-your-go-app-get-started-the-easy-way/


In Go, it is considered best practice to name a test file with the same  name as the file which contains the code being tested, with the added  suffix `_test`. 

Prefix of `Test` on the test function name is necessary so that the tool will pick it up as a valid test.

The latter part of the function name is generally the name of the function or method being tested.

Pass in the testing structure called `testing.T`, which allows for control of the test’s flow.

Table testing gets its name from its structure, easily represented by a  table with two columns: the input variable and the expected output  variable.

An interface is a named collection of methods, but also a variable type.

##### Golang Interface Mocking

```go
// readN reads at most n bytes from r and returns them as a string.
func readN(r io.Reader, n int) (string, error) {
	buf := make([]byte, n)
	m, err := r.Read(buf)
	if err != nil {
		return "", err
	}
	return string(buf[:m]), nil
}

// original interface
type Reader interface {
	   Read(p []byte) (n int, err error)
}

// mock
type ReaderMock struct {
	ReadMock func([]byte) (int, error)
}

func (m ReaderMock) Read(p []byte) (int, error) {
	return m.ReadMock(p)
}

func TestReadN_bufSize(t *testing.T) {
	total := 0
	mr := &MockReader{func(b []byte) (int, error) {
		total = len(b)
		return 0, nil
	}}
	readN(mr, 5)
	if total != 5 {
		t.Fatalf("expected 5, got %d", total)
	}
}
```

Table testing example:

```go
func Avg(nos ...int) int {
  sum := 0
  for _, n := range nos {
    sum += n
  }
  if sum == 0 {
    return 0
  }
  return sum / len(nos)
}

func TestAvg(t *testing.T) {
  type args struct {
    nos []int
  }
  tests := []struct {
    name string
    args args
    want int
  }{
    // TODO: Add test cases.
  }
  for _, tt := range tests {
    t.Run(tt.name, func(t *testing.T) {
      if got := Avg(tt.args.nos...); got != tt.want {
        t.Errorf("Avg() = %v, want %v", got, tt.want)
      }
    })
  }
}

// Test case example
{
  args: args{
    nos: []int{2, 2},
  },
  want: 2,
},
```
