#### How different types of fields are merged
How a particular field in a configuration file is merged with the live configuration depends on the type of the field. There are several types of fields:

primitive: A field of type string, integer, or boolean. For example, image and replicas are primitive fields. Action: Replace.

map, also called object: A field of type map or a complex type that contains subfields. For example, labels, annotations,spec and metadata are all maps. Action: Merge elements or subfields.

list: A field containing a list of items that can be either primitive types or maps. For example, containers, ports, and args are lists. Action: Varies.

When kubectl apply updates a map or list field, it typically does not replace the entire field, but instead updates the individual subelements. For instance, when merging the spec on a Deployment, the entire spec is not replaced. Instead the subfields of spec, such as replicas, are compared and merged.
