# Refererences
https://www.youtube.com/watch?v=IEe-5VOv0Js

# Key goals - Api design
1. simplicity/ease for consumption
2. flexible for change
3. secure   

# Guiding principles
## Put Users first 
1. Build for users of today but consider your platform/support/maintanance of tommorrow.
2. Interview users and use patterns of feedback as north star
3. work backwards model to have a shared product vision https://www.allthingsdistributed.com/2006/11/working_backwards.html
    1. Start by writing the Press Release
    2. Write FAQ
    3. Write User Manual
    4. Define the customer experience:
        1. For products with a user interface, we would build mock ups of each screen
        2. For web services, we write use cases, including code snippets, which describe ways you can imagine people using the product.
         
## Spent time to make API good - Built it for builders
	1) Predictable - Auto generate api doc and multi lang libraries
	2) simplicity - map to real life concepts, intuitive
	3) composable - Building blocks should fit together.
	4) backwards compatible by design
	5) avoid industry jargon and provide familiar, tactile parameter names
	6) nested structures to group related concepts rather than flattening them all to the top level
	7) Properties in the API are preferred as Enums rather than booleans.
	8) If an API request affects an object state, that change should be reflected in the response.
	9) consistent patterns for polymorphic APIs - rely on a type field to easily disambiguate what object you're going to be receiving
		and then provide parameters unique to that type of object and a designated nested structure.
	10)	Side effects and meaningful changes should be expressed through verbs. examples:
			/v1/payment_intents/:id/capture
			/v1/invoices/:id/mark_uncollectible

	11) API design process - multiple opportunities for feedback
		1. start with a design document that proposes a new API specification - details any new endpoints, API resources, events or changes to responses, parameters or webhook behavior.

		2. cross-functional review - platform, operations, our developer experience, our front-end tooling, security practices 
	    	1) in person to discuss questions like:
	    	"Are these changes consistent with the design patterns we have across our API?" 
	    	"Will the experience feel consistent when using different programming languages but the client libraries of Stripe?" "How will this look and feel to someone outside of Stripe?"
	    	"How might this break user expectations?" 
	    	"How might this change affect APIs that layer on top of the proposal?"
	    	"What's the friction for an existing or new user to adopt the API?" "Are we introducing a severe technical burden if you're migrating to it?"

		3) user testing to take feedback and release to limited set of users
		4) incrementally roll out the feature to users across the platform and keep a close eye for issues as they come up

	12. Dogfood api's internally to get better understanding
	share release with internal teams before launch
	ask teams to write up what we call a developer experience friction log

## Design your organization 
1. Conway's law
Organizations which design systems are constrained to produce designs which are copies of the communication structures of these organizations.

2. Basis no of teams work on a software system, there will tend to be separate software components. You can be intentional about how you build teams witin software engineering organization.

3. also build cultural and communication systems that support quickly building new APIs in a distributed and parallel fashion.
