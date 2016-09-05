# Ozero

[![Build Status](https://travis-ci.org/ANPez/Ozero.png)](https://travis-ci.org/ANPez/Ozero)
[![GoDoc](https://godoc.org/github.com/ANPez/Ozero?status.svg)](http://godoc.org/github.com/ANPez/Ozero)

Ozero is a thread pool for Go, focused on simplicity

# Usage example

```go
package main

import (
	"log"
	"time"

	"github.com/ANPez/Ozero"
)

func main() {
    nThreads := 10

	taskPool := ozero.NewPoolN(nThreads).AddWorkerFunc(func(data interface{}) {
		url := data.(string)
		log.Printf("Downloading URL: %s.", url)
		downloadOrPanic(url)
		log.Printf("Job finished ok")
	}).SetErrorFunc(func(data interface{}, err error) {
		log.Printf("Error while processing job in queue")
	}).SetShouldRetryFunc(func(data interface{}, err error, retry int) bool {
		switch err := err.(type) {
		case *types.HTTPError:
			return (err.StatusCode < 400) || (err.StatusCode >= 500)
		}
		return true
	}).SetTries(3).SetRetryDelay(time.Second)
}
```

## License
    Copyright 2016 Antonio Nicolás Pina

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.