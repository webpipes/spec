# WebPipes

## Terms

* _block_: a web service endpoint that implements the block API
* _block definition_: machine readable description of a block and its inputs and outputs
* _inputs_: named values for requests to a block
* _outputs_: named values for a block response
* _trigger_: a type of block with no inputs and implements the trigger registration API as well as the block API
* _action_: a type of block with no outputs that performs some "action"
* _pipeline_: an executable pipeline made from a pipeline definition and a pipeline executor
* _pipeline executor_: a web service that exposes a block API endpoint that executes a pipeline definition 
* _pipeline definition_: a block definition that also describes what blocks make it up and the pipes between them
* _pipe_: the connection of a block output to a block input
* _pipeline editor_: UI for composing a pipeline definition, e.x. Yahoo Pipes, IFTTT, etc
* _composite block_: a pipeline that is also a block
* _composite trigger_: a pipeline that is also a trigger
* _composite action_: a pipeline that is also an action


## Block Definition Format

	{
	    name: String
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
	<<< {"name": "Example block", "inputs": […], "outputs": […], … }

(Note http://zacstewart.com/2012/04/14/http-options-method.html)

### Trigger Registration API
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
	    inputs: {
      		… see block format …
	    }
	    outputs: {
      		… see block format …
	    }
	}


## Things to Prototype

- example triggers/blocks/actions
- pipeline executor 
- pipeline editor
- block client library, server framework/helper libs

## Authors
* Matthew Hudson
* Tom Robinson
* Jeff Lindsay