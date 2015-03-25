### Magritte Description Changes Requiring Action ###
  1. [SSProject>>otherEmailAddresses](ChangedMagritteDescriptionsSs3ToRc2#SSProject>>otherEmailAddresses.md)
  1. [SSRepository>>defaultEmailRecipients](ChangedMagritteDescriptionsSs3ToRc2#SSRepository>>defaultEmailRecipients.md)
### Benign Magritte Description Changes ###
  1. SSGroup>>members
  1. SSInstallWizzard>>storageClass, #wellKnownName, #cacheTimeout, #adminEmail
  1. SSMember>>information, #password
  1. SSProject>>parentProject, #developers, #guests, #defaultMoveTarget, #admins
  1. SSRepository>>rootUrl, #superUserEmail, #logo, #defaultCommitWriterClass
  1. WAEmailAddress>>username, #address

### Details ###
#### SSProject>>otherEmailAddresses ####
contents class changed from [SSEmailAddress to WAEmailAddress](ChangedMagritteDescriptionsSs3ToRc2#Transform_SSEmailAddress_to_WAEmailAddress.md).

New description:
```
descriptionOtherEmailAddresses

	^ MAToManyRelationDescription new
		classes: {WAEmailAddress};
		default: OrderedCollection new;
		label: 'Also send Emails to';
		selectorAccessor: #otherEmailAddresses;
		priority: 1500;
		yourself
```
Old description:
```
descriptionOtherEmailAddresses

	^ MAToManyRelationDescription new
		reference: SSEmailAddress new description;
		classes: {SSEmailAddress};
		default: OrderedCollection new;
		label: 'Also send Emails to';
		selectorAccessor: #otherEmailAddresses;
		priority: 1500;
		yourself
```
#### SSRepository>>defaultEmailRecipients ####
contents class changed from [SSEmailAddress to WAEmailAddress](ChangedMagritteDescriptionsSs3ToRc2#Transform_SSEmailAddress_to_WAEmailAddress.md).

New description:
```
descriptionDefaultEmailRecipients

	^ MAToManyRelationDescription new
		classes: {WAEmailAddress};
		default: OrderedCollection new;
		label: 'Default email recipients';
		selectorAccessor: #defaultEmailRecipients;
		priority: 1500;
		yourself
```
Old description:
```
descriptionDefaultEmailRecipients

	^ MAToManyRelationDescription new
		reference: SSEmailAddress new description;
		classes: {SSEmailAddress};
		default: OrderedCollection new;
		label: 'Default email recipients';
		selectorAccessor: #defaultEmailRecipients;
		priority: 1500;
		yourself
```
### Actions ###
#### Transform SSEmailAddress to WAEmailAddress ####
In both cases we are transforming a collection of SSEmailAddresses to a collection of WAEmailAddresses, so the following should do the trick:
```
SSRepository current projects do: [:project | 
	#(otherEmailAddresses defaultEmailRecipients) do: [:property |
		(project propertyIncludes: property)
			ifTrue: [ 
				project 
					propertyAt: property
					put: ((project propertyAt: property) 
						collect: [:ssEmailAddress | 
							WAEmailAddress 
								address: ssEmailAddress address
								username: ssEmailAddress username]) ]]].
```