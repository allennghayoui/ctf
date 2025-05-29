# GraphQL

## Finding GraphQL Endpoints
There is usually one endpoint on a web app that uses GraphQL that will be used for its requests. Common names for GraphQL endpoint (if you know the application is using GraphQL for sure from technology detection tools such as wappalyzer, you can try different combinations of keywords):
```
/graphql
/api/graphql
/api/v1/graphql
/graphql/graphql
/graphql/v1
/api
/api/v1
```

## Introspection and Schema
Burpsuite can generate Introspection queries automatically with the following steps:
	1. Within Repeater right-click the request body
	2. GraphQL > Set introspection query

### Probing For Introspection (is introspection enabled?)
```graphql
query {
	__schema {
		queryType {
			name
		}
	}
}
```

### Get Available Types with Names and Fields
```graphql
query GetTypesAndFieldsQuery {
	__schema {
		types {
			name
			description
			fields {
				name
			}
		}
	}
}
```

### Get Fields For Specified Type
```graphql
query GetFieldsForType {
	__type(name: "MyType") {
		name
		fields {
			name
			type {
				name
				kind
			}
		}
	}
}
```

### Enumerate Supported Queries
```graphql
query GetSupportedQueries {
	__schema {
		queryType {
			fields {
				name
				description
			}
		}
	}
}
```

### List Available Mutations
```graphql
query {
  __schema {
    mutationType {
      name
      fields {
        name
        args {
          name
          defaultValue
          type {
            ...TypeRef
          }
        }
      }
    }
  }
}

fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
              }
            }
          }
        }
      }
    }
  }
}
```
#### List Needed Arguments For Mutation
**Note: <NameOfArgumentObject> can be found from the result of listing the mutations above within the *ofType* field of the mutation**
```graphql
{   
  __type(name: "<NameOfArgumentObject>") {
    name
    inputFields {
      name
      description
      defaultValue
    }
  }
}

```

## Bypassing Introspection Defenses
### By Breaking Regex
Try inserting characters like spaces (or removing spaces), commas, and new lines after `__schema` as they are ignored by GraphQL but not by flawed regex.
```graphql
query NoSpaceQuery {
	__schema{
	}
}

query CommaQuery {
	__schema, {
	}
}

query NewLineQuery {
	__schema
{ }
}
```
### Using GET Method
`GET /graphql?query=query%7B__schema%0A%7BqueryType%7Bname%7D%7D%7D`

## Large Instrospection Query
```graphql
query IntrospectionQuery {
      __schema {
        queryType { name }
        mutationType { name }
        subscriptionType { name }
        types {
          ...FullType
        }
        directives {
          name
          description
          
          locations
          args {
            ...InputValue
          }
        }
      }
    }

    fragment FullType on __Type {
      kind
      name
      description
      
      fields(includeDeprecated: true) {
        name
        description
        args {
          ...InputValue
        }
        type {
          ...TypeRef
        }
        isDeprecated
        deprecationReason
      }
      inputFields {
        ...InputValue
      }
      interfaces {
        ...TypeRef
      }
      enumValues(includeDeprecated: true) {
        name
        description
        isDeprecated
        deprecationReason
      }
      possibleTypes {
        ...TypeRef
      }
    }

    fragment InputValue on __InputValue {
      name
      description
      type { ...TypeRef }
      defaultValue
    }

    fragment TypeRef on __Type {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
                ofType {
                  kind
                  name
                  ofType {
                    kind
                    name
                  }
                }
              }
            }
          }
        }
      }
    }
```

## Visualizing Instrospection Query with a Graph
1. Visit [GraphQL Voyager](https://graphql-kit.com/graphql-voyager/)
2. Select Change Schema
3. Select Introspection
4. Paste the result of the above InstrospectionQuery
5. Select Display

## Classic Attacks To Test on GraphQL
- IDOR
- XSS
- SQLi
- OS Command Injection
