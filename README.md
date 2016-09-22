# Alamofire+Synchronous

Synchronous requests for Alamofire

### Requirements

* iOS 9.0+ / Mac OS X 10.11+ / tvOS 9.0+ / watchOS 2.0+


* Xcode 8.0+
* Swift 3.0+

### Installation

For Alamofire 4.0+:

```ruby
pod 'Alamofire-Synchronous', '~> 4.0'
```

For Alamofire 3.0+:

``` ruby
pod 'Alamofire-Synchronous', '~> 3.0'
```

### Known issues

**If you execute synchronous requests from the main queue:**

The following tasks in the main queue after the synchronous request finished executation will be deferred to execute (including UI updates) because that you are executing the synchronous request from the main queue at the same time. On the other hand, in Alamofire 4,  New added parameters of  the methods of`downloadProgress` and `uploadProgres`: `queue`with the default value as`DispatchQueue.main`, therefore,  you need to reset the value of the new added parameters as Non-main queue.

example:

```swift
// from the main queue (**not recommended**):
let response = Alamofire.download("https://httpbin.org/stream/100", method: .get, to: destination).downloadProgress { progress in
    	// Codes at here will be delayed before the synchronous request finished running.
    	print("Download Progress: \(progress.fractionCompleted)")
    
    }.response()
    
if let error = response.error {
    print("Failed with error: \(error)")
}else{
    print("Downloaded file successfully")
}
```

```swift
// from the main queue (**not recommended**):
let response = Alamofire.download("https://httpbin.org/stream/100", method: .get, to: destination).downloadProgress(queue: DispatchQueue.global(qos: .default)) { progress in
		// Codes at here will not be delayed
        print("Download Progress: \(progress.fractionCompleted)")
    
        DispatchQueue.main.async {
            // code at here will be delayed before the synchronous finished.
        }
    
    }.response()
    
if let error = response.error {
    print("Failed with error: \(error)")
}else{
    print("Downloaded file successfully")
}
```



### Usage

```swift
import Alamofire
import Alamofire_Synchronous
```

**The usage differences between Alamofire and Alamofire_Synchronous**: The parameters of `queue` and  `completionHandler` in the response* methods simply need to be removed out by using the returned value from the response* methods.



Example(For Alamofire 4.0+):

``` swift
//get request and response json
let response = Alamofire.request("https://httpbin.org/get", parameters: ["foo": "bar"]).responseJSON()
if let json = response.result.value {
	print(json)
}

// post request and response json(with default options)
let response = Alamofire.request("https://httpbin.org/post", method: .post, parameters: ["foo": "bar"]).responseJSON(options: .allowFragments)
if let json = response.result.value {
    print(json)
}

// download
let response = Alamofire.download("https://httpbin.org/stream/100", method: .get, to: destination).response()
if let error = response.error {
    print("Failed with error: \(error)")
}else{
    print("Downloaded file successfully")
}
```

For more usage, see [Alamofire's documents](https://github.com/Alamofire/Alamofire#usage).

### License

See LICENSE for details.
