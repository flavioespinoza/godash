# godash
Lodash inspired utility library delivering modularity, performance & extras.

## Installation
```bash
go get -u github.com/flavioespinoza/godash
```

```bash
git init
git add README.md
git commit -m "first commit"
git remote add origin https://github.com/flavioespinoza/godash.git
git push -u origin master
```

## Methods

- [x] Map
- [x] Reduce
- [x] Filter
- [x] Union
- [x] Intersection
- [x] Xor
- [x] Difference
- [x] IndexOf

## Examples

#### Count words

```go

	package main
	
	import(
		"fmt"
		"github.com/flavioespinoza/godash"
	)
	
	func Main(){
		
		// Counting words
		const (
			words = "What's the most you ever lost on a coin toss?"
		)
	    
		var result map[string]int
		err:=godash.In(strings.Split(words," ")).
			Map(func(word string)string{
			return strings.Trim(strings.Trim(word,"\n\t "),".,!")
		}).
			Filter(func(word string)bool{
			return word!=""
		}).
			Reduce(func(Map map[string]int,word string)map[string]int{
			Map[word] = Map[word]+1
			return Map
		},map[string]int{}).
			Out(&result)
		fmt.Println(err)
		fmt.Println(result["coin"])
		fmt.Println(result["the"])
		
	    // Output:
		// <nil>
		// 6
		// 3
	}
```
#### Map Filter Reduce to calculate an bill

```go

	package main
	
	import(
		"fmt"
		"github.com/flavioespinoza/godash"
	)
	
	func Main(){
		var total int
		type Order struct{
			Product string
			Quantity int
			UnitPrice int
			InStock bool
		}
		orders:=[]Order{
			{"Iphone", 2, 500,true}, 
			{"Graphic card", 1, 250,true}, 
			{"Flat screen", 3, 600,true},
			{"MacBook White",2,400,false},
			{"Ipad air", 5, 200,true},
		}
		err:=godash.In(orders).
			Filter(func(order Order)bool{
				return order.InStock
			}).
			Map(func(order Order,index int)int{
				return order.Quantity * order.UnitPrice
			}).
			Reduce(func(total int,subtotal int)int{
				return total + subtotal
			},0).
			Out(total)
			
		fmt.Print(err,total)
		
		// Output:
		// <nil> 4050
	}
```

#### Difference between 2 slices
	
```go
	package main
	
	import(
		"fmt"
		"github.com/flavioespinoza/godash"
	)
	
	func Main(){
	
		var difference []string
		err := godash.Difference([]string{"a", "b", "c", "d"}, []string{"a", "c", "x"}, &difference)
		fmt.Println(err)
		fmt.Println(difference)
		
		// Output:
		// <nil>
		// [b d]
	}
```	
#### Filter out odd numbers

```go
	package main
	
	import(
		"fmt"
		"github.com/flavioespinoza/godash"
	)
	
	func Main(){
		var evenNumbers []int
		err := godash.Filter([]int{0, 1, 2, 3, 4}, func(element int) bool {
			return element % 2 == 0
		}, &evenNumbers)
		
		fmt.Println(err)
		fmt.Println(evenNumbers)
		
		// Output:
		// <nil>
		// [0 2 4]
	}

```
#### Compute the mean of an array of integers
	
```go
	package main

	import "github.com/flavioespinoza/godash"
	import "fmt"
		
	func Main(){
		var mean int
		err := godash.Reduce([]int{10, 20, 30}, func(result int, element int, index int, collection []int) int {
			if l := len(collection); l-1 == index {
				return (result + element) / l
			} else {
		        return result + element
			}
		}, 0, &mean)
		
		fmt.Println(mean)
		fmt.Println(err)
		
		// Output:
		// 20
		// <nil>
	}
```
		
#### Grouping adults by country using a pipeline

```go
	package main
	
	import "github.com/flavioespinoza/godash" 
	import "fmt"
	
	func Main(){
		// Group people by country
		// Demonstrates the use of Reduce to transform an collection
		// into a map
		type Person struct {
	   		Name    string
			Country string
			Age     int
	   	}
				
	    var adultPeopleByCountry map[string]int
		err := godash.In([]Person{
			{"John", "France", 18},
			{"Jane", "England", 16},
			{"Jack", "France", 20},
			{"Anna", "Spain", 19},
			{"Eduardo", "Spain", 30},
			{"Michel", "France", 12}}).
			Filter(func(person Person) bool { return person.Age >= 18 }).
			Reduce(func(list map[string]int, person Person) map[string]int {
				list[person.Country] = list[person.Country] + 1
				return list
			}, map[string]int{}).
			Out(&adultPeopleByCountry)
		
		fmt.Println(err)
		fmt.Println(adultPeopleByCountry["France"])
		fmt.Println(adultPeopleByCountry["Spain"])
		fmt.Println(adultPeopleByCountry["England"])
		
		// Output:
		// <nil>
		// 2
		// 2
		// 0
	}
```

#### Using a pipeline to compute the sum of all people in countries

```go
	package main
	
	import "github.com/flavioespinoza/godash"
	import "fmt"
	
	func Main(){
		type Country struct {
			Name       string
		    Population int
		}
		var total int
		err := godash.In([]Country{{"France", 1000}, {"Spain", 5000}}).
			Map(func(country Country) int { return country.Population }).
			Reduce(func(total int, count int) int { return total + count }, 0).
			Out(&total)
		
		fmt.Println(err)
		fmt.Println(total)
		
		// Output:
		// <nil>
		// 6000
	}
```
