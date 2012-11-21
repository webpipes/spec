# WebPipes Spec
Version: 0.1 draft

## Overview
WebPipes is an open framework for turning the web into an end-user programming environment. It provides set of standards letting the developer ecosystem create primitives called blocks that can be wired together to create executable pipelines. Pipelines can be as simple IFTTT recipes, or as complex as Yahoo Pipes, but they can also be blocks themselves and used in simpler or more complex pipelines. WebPipes is Turing Complete.

> I'm not presumptuous enough to say this is finally "pipes for the web", but I certainly think "web pipes" is the most appropriate name for what this is. *--Jeff Lindsay*

## Terms
* __block__: a web service endpoint that implements the block API
* __block definition__: machine readable description of a block and its inputs and outputs
* __inputs__: named values used in requests to a block
* __outputs__: named values used in a block response
* __trigger__: a type of block with no inputs and implements the trigger registration API as well as the block API
* __action__: a type of block with no outputs that performs some "action"
* __pipeline__: an executable series of blocks made from a pipeline definition and a pipeline executor
* __pipeline executor__: a web service that exposes a block API endpoint that executes a pipeline definition 
* __pipeline definition__: a block definition that also describes what blocks make it up and the pipes between them
* __pipe__: the connection of a block output to a block input
* __pipeline editor__: UI for composing a pipeline definition, e.x. Yahoo Pipes, IFTTT, etc
* __composite block__: a pipeline that is also a block
* __composite trigger__: a pipeline that is also a trigger
* __composite action__: a pipeline that is also an action

## Block Definition Format

	{
	    name: String
	    description: String
	    input: {
	        name: String
	        type: String (Array, String, Number, Boolean)
	        description: String
	        optional?: Boolean
	        default?: Any
	    }
	    output: {
	        name: String
	        type: String (Array, String, Number, Boolean)
	        description: String
	    }
	}
    

## Block API
### Input and Output
Block endpoints work with JSON objects containing input or output "records". An input or output record is a JSON object with keys corresponding to the defined inputs or outputs of the block. Given a block with two inputs "foo" and "bar", here is an example input record:
	
	{"foo": "string value for foo", "bar": 42}

This is put inside an input envelope, which is just a single key JSON object with the key "input":

	{"input": {"foo": "string value for foo", "bar": 42}}

Similarly, block endpoints then respond with an envelope containing output records. Given a block with one output "baz", here is an example of a single output record inside an output envelope:

	{"output": [{"baz": "some value for baz"}]}

Notice the value of output is an array. This is because you may get back multiple output records, like this: 

	{"output": [{"baz": "some value for baz"}, {"baz": "some other value for baz"}]}

### Using a block
A block endpoint has normal HTTP semantics, but using input and output data structures. The request body should be empty or be a valid input envelope with an input record, and the response body should be empty or be a valid output envelope with one or more output records. The input and output records must have keys that match the inputs and outputs defined in the block definition.

	>>> POST /block-endpoint
	>>> Content-Type: application/json
	>>> …more headers…
	>>> {"input": {"foo": "Value for foo input", "bar": "Value for bar input"}}

	<<< 200 OK
	<<< Content-Type: application/json
	<<< …more headers…
	<<< {"output": [{"baz": "Value for baz output", "qux": "Value for qux input"}]}

### Getting a block definition
The block definition is exposed in the body of response to an OPTIONS request on the block endpoint.

	>>> OPTIONS /block-endpoint
	>>> …headers…

	<<< 200 OK
	<<< Content-Type: application/json
	<<< …more headers…
	<<< {"name": "Example block", "input": […], "output": […], … }

(Note http://zacstewart.com/2012/04/14/http-options-method.html)

## Trigger Registration API
The trigger registration API is used for registering an HTTP callback that triggers the entry action of a pipeline. It's based on the tentative HTTP Subscriptions standard (loosely based on PubSubHubbub). It's only available on blocks that are triggers.

	>>> GET /block-endpoint
	>>> Expect: subscription
	>>> X-Callback: http://pipeline-endpoint?; method="POST"
	>>> …more headers…

## Pipeline Definition Format

	{
	    name: String
	    description: String
	    blocks: [
	        {
	            id: Number			# opaque id for block in pipeline
	            url: String		# url for block endpoint
	        }
	    ]
	    pipes: [
	        {
	            source_block: Number			# block id for source
	            source_output: String		# name of output from source
							source_value: Any				# value if no source block
	            target_block: Number			# block id for target
	            target_input: String			# name of input for target
	        }
	    ]
	    input: {
      		… see block format …
	    }
	    output: {
      		… see block format …
	    }
	}


## Things to Prototype

- triggers/blocks/actions
- pipeline executor 
- pipeline editor

## Authors
* Matthew Hudson
* Tom Robinson
* Jeff Lindsay