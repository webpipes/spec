# WebPipes

## Terms

- block: a web service endpoint that implements the block API
- block definition: machine readable description of a block and its inputs and outputs
- inputs: named values for requests to a block
- outputs: named values for a block response
- trigger: a type of block with no inputs and implements the trigger API as well as the block API
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

POST http://block-endpoint
    => JSON object of input data
    <= JSON object/array of output data
OPTIONS http://block-endpoint
    <= JSON object of block def

http://zacstewart.com/2012/04/14/http-options-method.html

Triggers use tentative HTTP Subscriptions spec or some simple variation.


## Pipeline Definition Format

	{
	    name: String
	    description: String
	    blocks: [
	        {
	            id: Number
	            url: String
	        }
	    ]
	    pipes: [
	        {
	            source_block: Number
	            source_output: String
	            target_block: Number
	            target_input: String
	        }
	    ]
	    inputs: {
        
	    }
	    outputs: {
        
	    }
	}


## Things to Prototype

- example triggers/blocks/actions
- pipeline executor
- pipeline editor
- block client library, server framework/helper libs