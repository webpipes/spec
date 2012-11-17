# WebPipes

## Terms

- block: a web service endpoint that implements the block API
- block definition: machine readable description of a block and its inputs and outputs
- inputs: named values for requests to a block
- outputs: named values for a block response
- trigger: a type of block with no inputs and implements the trigger registration API as well as the block API
- action: a type of block with no outputs that performs some "action"
- pipeline: a composite block made from a pipeline definition and a pipeline executor
- pipeline executor: a web service that exposes a block API endpoint that executes a pipeline definition 
- pipeline definition: a block definition that also describes what blocks make it up and the pipes between them
- pipe: the connection of a block output to a block input
- pipeline editor: UI for composing a pipeline definition, e.x. Yahoo Pipes, IFTTT, etc
- composite block: a pipeline used as a block
- composite trigger: a pipeline used as a trigger
- composite action: a pipeline used as an action


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

### Using a block
Normal HTTP semantics, but the request body is a JSON object of input data, and the response body is a JSON object of output data.

	>>> POST /block-endpoint
	>>> …headers…
	>>> {"foo": "Value for foo input", "bar": "Value for bar input"}

	<<< 200 OK
	<<< …headers…
	<<< {"baz": "Value for baz output", "qux": "Value for qux input"}

### Getting a block definition
The block definition is exposed in the body of response to an OPTIONS request.

	>>> OPTIONS /block-endpoint
	>>> …headers…

	<<< 200 OK
	<<< …headers…
	<<< {"name": "Example block", … }

(Note http://zacstewart.com/2012/04/14/http-options-method.html)

## Trigger Registration API
The trigger registration API is used for registering an HTTP callback that triggers the entry point action of a pipeline. It's based on the tentative HTTP Subscriptions standard (loosely based on PubSubHubbub).

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