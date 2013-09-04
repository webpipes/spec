# WebPipes Spec
Version: 0.2 draft

## Contents
* [Overview](#overview)
* [Terminology](#terminology)
* [Block Definition Format](#block-definition-format)
* [Block API](#block-api)
	* [Input and Output](#input-and-output)
	* [Using a Block](#using-a-block)
	* [Getting a Block Definition](#getting-a-block-definition)
* [Trigger Registration API](#trigger-registration-api)
* [Pipeline Definition Format](#pipeline-definition-format)
* [Authors](#authors)

## Overview
WebPipes is an open framework for turning the web into an end-user programming environment. It provides set of standards letting the developer ecosystem create primitives called blocks that can be wired together to create executable pipelines. Pipelines can be as simple as IFTTT recipes, or as complex as Yahoo Pipes, but they can also be blocks themselves and used in simpler or more complex pipelines. WebPipes is Turing Complete.

> I'm not presumptuous enough to say this is finally "pipes for the web", but I certainly think "web pipes" is the most appropriate name for what this is. *--Jeff Lindsay*

## Terminology
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
A Block Definition is a JSON object describing the core behavior of a Block. A [Block Definition JSON Schema](https://github.com/webpipes/spec/blob/master/schemas/block-definition.schema.json) is available for validating implementations.

	{
	    name: String
	    url: String
	    description: String
	    inputs: {
	        name: String
	        type: String (Array, String, Number, Boolean)
	        description: String
	        optional?: Boolean
	        default?: Any
	    }
	    outputs: {
	        name: String
	        type: String (Array, String, Number, Boolean)
	        description: String
	    }
	}


## Block API
### Input and Output
Block endpoints work with JSON objects containing input or output "records". An input or output record is a JSON object with keys corresponding to the defined inputs or outputs of the block. Given a block with two inputs "foo" and "bar", here is an example input record:
	
	{"foo": "string value for foo", "bar": 42}

This is put inside an input envelope, which is just a single key JSON object with the key "inputs":

	{"inputs": {"foo": "string value for foo", "bar": 42}}

Similarly, block endpoints then respond with an envelope containing output records. Given a block with one output "baz", here is an example of a single output record inside an output envelope:

	{"outputs": [{"baz": "some value for baz"}]}

Notice the value of output is an array. This is because you may get back multiple output records, like this: 

	{"outputs": [{"baz": "some value for baz"}, {"baz": "some other value for baz"}]}

### Using a Block
A block endpoint has normal HTTP semantics, but using input and output data structures. The request body should be empty or be a valid input envelope with an input record, and the response body should be empty or be a valid output envelope with one or more output records. The input and output records must have keys that match the inputs and outputs defined in the block definition.

	>>> POST /block-endpoint
	>>> Content-Type: application/json
	>>> …more headers…
	>>> {"inputs": {"foo": "Value for foo input", "bar": "Value for bar input"}}

	<<< 200 OK
	<<< Content-Type: application/json
	<<< …more headers…
	<<< {"outputs": [{"baz": "Value for baz output", "qux": "Value for qux input"}]}

### Getting a Block Definition
The block definition is exposed in the body of response to an HTTP request using the [OPTIONS method](http://zacstewart.com/2012/04/14/http-options-method.html) on the block endpoint.

	>>> OPTIONS /block-endpoint
	>>> …headers…

	<<< 200 OK
	<<< Content-Type: application/json
	<<< …more headers…
	<<< {"name": "Example block", "inputs": {…}, "outputs": […], … }

## Trigger Registration API
The trigger registration API is used for registering an HTTP callback that triggers the entry action of a pipeline. It's based on the tentative HTTP Subscriptions standard (loosely based on PubSubHubbub). It's only available on blocks that are triggers.

	>>> GET /block-endpoint
	>>> Expect: subscription
	>>> X-Callback: http://pipeline-endpoint?; method="POST"
	>>> …more headers…

## Pipeline Definition Format
The Pipeline Definition is a JSON object describing what blocks make it up and the pipes between them. A [Pipeline Definition JSON Schema](https://github.com/webpipes/spec/blob/master/schemas/pipeline-definition.schema.json) is available for validating implementations.

	{
	    name: String
	    description: String
	    blocks: [
	        {
	            id: Number		# opaque id for block in pipeline
	            url: String		# url for block endpoint
	        }
	    ]
	    pipes: [
	        {
	            source_block: Number			# block id for source
	            source_output: String			# name of output from source
	            source_value: Any				# value if no source block
	            target_block: Number			# block id for target
	            target_input: String			# name of input for target
	        }
	    ]
	    inputs: {
      		… see block format …
	    }
	    outputs: {
      		… see block format …
	    }
	}

## Authors
* Matthew Hudson
* Tom Robinson
* Jeff Lindsay
