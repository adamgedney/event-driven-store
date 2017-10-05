# redux-observable-source

Key  Data Flow Takeaways:
	1. Components only announce they need data
	2. Components can not directly dispatch for data. (Eliminates the need for Thunks or Sagas, reducing app complexity)
	3. AJAX calls are made via the same fn and the response is fed to the Observable
	4. Websocket pushes are fed to the same Observable getting AJAX responses
	5. An Event Manager [ An Adapter ] is responsible for mapping data to branches in the State Tree
	6. State Tree data is fed to components via branches.


Data Mapping Considerations:
Model Mapping Paradigm (won't work. Too many possible bugs when Model schema's match)
If a Model type is predefined for a particular type of Event, then there can be a simple Adapter that maps Models to a branch on the tree

User:{
	Name,
	id
}

adapterMap:{
	myEliBranch: 'User' // User is the Model we will look for
}

// Adapter is setup with Model mappings, then Data is piped through it, the output becomes a keyed branch
  Adapter(adapterMap)( ${name: 'Eli',id: 0} )
        return myEliBranch:{name: 'Eli',id: 0}

Keyed Response Paradigm
 Any data fed to the Observable has to be keyed so the Adapter knows how to map data to the right branch.

getUserById:{
	Name,
	id
}

adapterMap:{
	user : 'getUserById' // User is the keywe will look for
}

Any data Observed that has a key of 'getUserById' will be pushed onto the 'user' branch of the tree

Problem: Responses all need to be keyed. Ajax responses and socket pushes need to be keyed.
Pros: Works great with graphql responses

Tips: Easiest way to key ajax responses for non graphql responses is to use a helper fn to make the call where the responseKey is passed in, then the ajax response gets built using it



If adapterMap were a collection, then the adapter could use either method, keyed response or Model as a fallback mapping, to decipher data
adapterMap:[
	{
	dataKey: 'get UserById',
	branch : 'user',
	model : 'User'
	}
]
