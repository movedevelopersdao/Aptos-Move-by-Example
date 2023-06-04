# Object

The _Object model_ allows Move to represent a complex type as a set of resources stored within a single address and offers a rich capability model that allows for fine-grained resource control and ownership management.

**Properties of Object model**

* Simplified storage interface that supports a heterogeneous collection of resources to be stored together. This enables data types to share a common core data layer (e.g., tokens), while having richer extensions (e.g., concert ticket, sword).
* Globally accessible data and ownership model that enables creators and developers to dictate the application and lifetime of data.
* Extensible programming model that supports individualization of user applications that leverage the core framework including tokens.
* Support emitting events directly, thus improving discoverability of events associated with objects.
* Considerate of the underlying system by leveraging resource groups for gas efficiency, avoiding costly deserialization and serialization costs, and supporting deletability.

{% hint style="info" %}
Object is a core primitive in Aptos Move and created via the object module at 0x1::object
{% endhint %}

