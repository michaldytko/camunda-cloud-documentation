---
title: Creating readable process models
tags:
    - BPMN
---

We create visual process models to better understand, discuss, and remember processes. Hence, it is crucial that models are easy to read and understand. The single most important thing is to to use well-chosen labels.

## Essential practices

### Labeling BPMN elements

Use [conventions for naming BPMN elements](naming-bpmn-elements.md); this will consistently inform the reader of the business semantics. The clarity and meaning of a process is often only as good as its labels.

<div bpmn="best-practices/creating-readable-process-models-assets/TwitterDemoProcess.bpmn" callouts="start_event_new_tweet,user_task_review_tweet,gateway_approved,boundary_event_tweet_duplicated,end_event_tweet_published" />

<span className="callout">1</span> 

*Start event* labels informs the reader of how the process is *triggered*.

<span className="callout">2</span>

An *activity* - labeled as "activity" - informs the reader of the piece of *work* to be *carried out*.

<span className="callout">3</span>

*Gateway* labels clarifies based on which condition(s) and along *which sequence flow* the process proceeds.

<span className="callout">4</span>

Labeled *boundary events* clearly express in which cases a process execustion might follow an *exceptional path*.

<span className="callout">5</span>

Labeled *end events* characterize end *results* of the process from a business perspective.

## Recommended practices

### Modeling symmetrically

Try to model symmetrically. Identify related splitting and joining gateways and form easily recognizable *visual*, eventually *nested*, *blocks* with those gateways.

<div bpmn="best-practices/creating-readable-process-models-assets/modeling-symmetrically.bpmn" callouts="inclusive_gateway_splitting,inclusive_gateway_joining,exclusive_gateway_splitting,exclusive_gateway_joining" />

:::caution Camunda Platform 7 Only
Inclusive Gateways (OR) are [not yet supported in Camunda Cloud](https://docs.camunda.io/docs/reference/bpmn-processes/bpmn-coverage/)
:::

<span className="callout">1</span> 

The inclusive gateway splits the process flow into two paths which are ...

<span className="callout">2</span> 

... joined again with an inclusive gateway. Inside that block ...

<span className="callout">3</span>

another exclusive gateway splits the process flow into two more paths which are ...

<span className="callout">4</span>

... joined again with an exclusive gateway.

By explicitly showing *pairs of gateways* "opening" and "closing" parts of the process diagram, and by positioning such gateway pairs *as symmetrically as possible*, the readability of process model is improved. The reader can easily recognize logical parts of the diagram and quickly jump to those parts the reader is momentarily interested in.

### Modeling from left to right

Model process diagrams *from left to right*. By carefully positioning symbols from left to right, according to the typical point in time at which they occur, one can improve the readability of process models significantly:

<div bpmn="best-practices/creating-readable-process-models-assets/model-from-left-to-right.bpmn" />

Modeling from left to right supports the reading direction (for western audience) and supports the human field of vision - which prefers wide screens. 

### Creating readable sequence flows

Consciously decide whether *overlapping sequence flows* make your model more or less readable. On one hand, avoid overlapping sequence flows where the reader will not be able to follow the flow directions anymore. Use overlapping sequence flows where it is less confusing for the reader to see just one line representing several sequence flows leading to the same target.

Avoid sequence flows *violating the reading direction*, meaning no outgoing flows on the left or incoming flows on the right of a symbol.

<div bpmn="best-practices/creating-readable-process-models-assets/overlapping-sequence-flows.bpmn" callouts="end_event_order_declined,task_correct_fax_number" />

<span className="callout">1</span>

The author could have made the five (!) sequence flows leading into the end event visible by separating them. However, by consciously choosing to partly overlap those flows, this model becomes less cluttered, therefore less confusing and easier to read.

<span className="callout">2</span>

The author could have attached the sequence flow, leaving this task on its left. However, this would have decreased readability, because the flow connection violates the reading direction. The same applies to incoming flows on the right of a symbol.

*Avoid flows crossing each other* and *flows crossing many pools or lanes*, wherever possible. Rearrange the order of lanes and paths to make your sequence flows more readable. Oftentimes, removing lanes can improve readability! Rearrange the order of pools in a collaboration diagram to avoid message flows crossing pools as much as possible. Often, you will find a "natural" order of pools reflecting the order of first involvement of parties in the end-to-end process. This order will often also lead to a minimum of crossing lines.

*Avoid very long (multi page) sequence flows*, especially when flowing against the reading direction. The reader will lose any sense of what such lines actually mean. Instead, use link events to connect points which are not on the same page or screen anymore.

<div bpmn="best-practices/creating-readable-process-models-assets/avoiding-multi-page-sequence-flows.bpmn" callouts="throwing-linkevent-recourse-not-possible,catching-linkevent-recourse-not-possible" />

:::caution Camunda Platform 7 Only
Link events are [not yet supported in Camunda Cloud](https://docs.camunda.io/docs/reference/bpmn-processes/bpmn-coverage/)
:::

<span className="callout">1</span>

You see a throwing link event here, which...

<span className="callout">2</span>

...directly links to a catching link event just as if the sequence flow would have been connected.

Avoid excessive use of link events. The example above serves to show the possible usage, but at the same time, it is too small to satisfy the usage of link events in real-world sceanrio!

### Modeling explicitly

Make your models easier to understand by modeling *explicitly*, which most often means to either completely avoid certain more "implicit" BPMN constructs, or at least to use them cautiously. Always consider the central *goal of increased readability* and understandability of the model when deciding whether to model explicitly or implicitly. When in doubt, it's best to favor an explicit style.

#### Using gateways instead of conditional flows

Model splitting the process flow by always using *gateway symbols* like <img src="/img/bpmn-elements/inclusive-gateway.svg" className="inline-image" /> instead of conditional flows <img src="/img/bpmn-elements/conditional-flow.svg" className="inline-image" />.

<div bpmn="best-practices/creating-readable-process-models-assets/explicit-gateways-instead-of-conditional-flows.bpmn" callouts="inclusive_gateway" />

:::caution Camunda Platform 7 Only
Inclusive Gateways (OR) and Conditional sequence flows are [not yet supported in Camunda Cloud](https://docs.camunda.io/docs/reference/bpmn-processes/bpmn-coverage/)
:::

<span className="callout">1</span>

For example, you could've left out this inclusive gateway by drawing two outgoing sequence flows directly out of the preceding task **Choose menu** and attaching conditions to those sequence flows (becoming conditional sequence flows <img src="/img/bpmn-elements/conditional-flow.svg" className="inline-image" />). However, experience shows that readers understand the flow semantics of gateways better, which is why we do not make use of this possibility.


#### Modeling start and end events

Model the trigger and the end status of processes by always explicitly showing the *start* and *end event symbols*.

<div bpmn="best-practices/creating-readable-process-models-assets/explicit-start-and-end-events.bpmn" callouts="start_event, end_event" />

:::caution
Process models without start and end event cannot be executed on the Camunda workflow engine
:::

<span className="callout">1</span>

According to the BPMN standard, you could have left out the start event...

<span className="callout">2</span>

...as long as you also leave out the end events of a process. However, you would have lost important information in your model, which is why we do not make use of this syntactical possibility.

Be specific about the *state* you reached with your event from a *business perspective*. Quite typically, you will reach "success" and "failure" like events from a business perspective:

<div bpmn="best-practices/creating-readable-process-models-assets/business-states.bpmn" callouts="invoice_paid, invoice_rejected" />

<span className="callout">1</span>

'Invoice paid' better qualifies the "successful" business state than e.g. 'Invoice processed' would...

<span className="callout">2</span>

...because in principle, you can call the failed state 'Invoice processed', too, but the reader of the diagram is much better informed by calling it 'Invoice rejected'.

#### Separating splitting and joining gateways

In general, avoid mixing up the split and join semantics of gateways by explicitly showing *two separate symbols*:

<div bpmn="best-practices/creating-readable-process-models-assets/separation-of-splitting-and-joining-gateways.bpmn" callouts="exclusive_gateway_joining,exclusive_gateway_splitting_2" />

<span className="callout">1</span>

You could have modeled this join implicitly by leaving out the explicitly joining XOR gateway and directly connecting two incoming sequence flows to...

<span className="callout">2</span>

...the subsequent splitting XOR gateway. Of course, BPMN would allow this for other gateway types, too. However, experience shows that readers will often overlook the join semantics of such gateways serving two purposes at the same time.

The fact that readers will often overlook the join semantics of gateways serving to join as well as split the process flow at the same time, combined with the preference for [modeling symmetrically](#modeling-symmetrically), leads us to prefer *splitting and joining gateways modeled with separate symbols*.

However, there are cases in which the readability of models can be improved with *implicit modeling*. Consider the following example:

<div bpmn="best-practices/creating-readable-process-models-assets/without-separation-of-splitting-and-joining-gateways.bpmn" callouts="event_based_gateway" />

<span className="callout">1</span>

The *primary function* of this gateway from a reader's perspective is to *split* the process flow based on events. However, it is also *joining* two sequence flows. You could explicitly model that join by introducing a separate joining XOR gateway directly before the event-based gateway. We argue that this will not increase readability, but rather decrease it.

A decrease in readability by modeling explicit joining gateways often happens when *modeling loops*, because many readers will be slightly irritated by a joining gateway which is visually placed before the splitting gateway "belonging" to the same block. It is then often preferable to just use implicit joining to avoid unnecessary irritations.

#### Using XOR gateway markers

Model the XOR gateway by explicitly showing the **X** symbol, even if some tools allow to draw a blank gateway.

<div bpmn="best-practices/creating-readable-process-models-assets/explicit-xor-gateway-markers.bpmn" callouts="exclusive_gateway_splitting, exclusive_gateway_joining" />

<span className="callout">1</span>

You could have shown the splitting gateway...

<span className="callout">2</span>

...as well as the joining gateway without the **X** symbol indicating that it is an exclusive gateway.

The **X** marker makes a clearer difference to the other gateway types (inclusive, parallel, event-based, complex) which leads us to prefer *explicit XOR gateway markers* in general.

#### Splitting sequence flows with parallel gateways

Always model splitting the process flow by explicitly showing the *gateway symbol*:

<div bpmn="best-practices/creating-readable-process-models-assets/explicit-splitting-parallel-gateways.bpmn" callouts="parallel_gateway_splitting,parallel_gateway_joining" />

<span className="callout">1</span>

You could have modeled this parallel split implicitly by leaving out the gateway and drawing two outgoing sequence flows out of the preceding task **Choose menu**. However, the reader needs deeper BPMN knowledge in order to understand this model. Additionally, for joining the parallel flows...

<span className="callout">2</span>

...you will always need the explicit symbol.

The fact that readers of models using parallelization will likely need to understand the semantics of a parallel join combined with the preference for modeling symmetrically leads us to prefer *explicit parallel gateways*, too.


#### Joining sequence flows with XOR gateways

Model joining the process flow by explicitly showing the *XOR gateway symbol* so the reader does not have to know BPMN details to understand how two incoming or outgoing sequence flows in a task behave. Additionally, this often supports the [symmetry of the model](#modeling-symmetrically) by explicitly showing a "relationship" of the splitting and joining *gateways forming a visual "block"*.

<div bpmn="best-practices/creating-readable-process-models-assets/explicit-joining-xor-gateways.bpmn" callouts="exclusive_gateway_joining,exclusive_gateway_splitting" />

<span className="callout">1</span>

You could have modeled this join implicitly by leaving out the gateway and directly connecting the two incoming sequence flows to the subsequent task **Have lunch**. However, explicitly modeling the join better visualizes a block, the joining gateway semantically "belongs" to...

<span className="callout">2</span>

...the earlier split. In case the reader is not interested in the details of dinner preparation but just in having dinner, it's easy to "jump" to the gateway, "closing" that logical part of the model.

This is particularly helpful for models bigger than that example with many such (eventually nested) blocks. Consider the following model, showing two *nested blocks* of gateways:

<div bpmn="best-practices/creating-readable-process-models-assets/explicit-joining-gateways-nested.bpmn" callouts="exclusive_gateway_joining" />

<span className="callout">1</span>

Now, you couldn't have modeled this join implicitly, because it's directly followed by an inclusive gateway with very different join semantics. *Consistency* of joining techniques is another reason why we prefer explicitly joining sequence flows in general.

There are always exceptions to the rule! There are cases in which the readability of models can be *improved* with *implicit modeling*. So don't be dogmatic about explicit modeling; always aim for the most readable model. The following example shows a case of a model in which splitting and joining points do not form natural "blocks" anyway. In such cases, it can be preferable to make use of *implicit joining* to improve the overall readability!

<div bpmn="best-practices/creating-readable-process-models-assets/implicit-joining-gateways.bpmn" callouts="task_hand_over_to_collection_agency,task_close_case" />

<span className="callout">1</span>

You could have modeled the implicit join happening at this task with an exclusive joining gateway. However, here one might decide that the implicit join contributes to readability.

<span className="callout">2</span>

Again, you could have modeled the implicit join explicitly, too. As this again would not visualize a simple "block" of gateways, we often prefer to leave it out.

### Avoiding lanes

Consider *avoiding lanes* for most of your models all together. They tend to conflict with several of the best practices presented here, like [Modeling *Symmetrically*](#modeling-symmetrically), [Emphasizing the *Happy Path*](#emphasizing-the-happy-path) and [Creating Readable *Sequence Flows*](#creating-readable-sequence-flows). Apart from readability concerns, our experience also shows that lanes make it more difficult to change the resulting process models and therefore cause considerably *more effort in maintenance*.

When modeling on an *operational level*, where showing the responsibility of roles matters most, we recommend to [use *collaboration diagrams*](#using-collaboration-diagrams) with several *separate pools* for the process participants instead of lanes.

However, the usage of lanes might be meaningful for:

* *Strategic* level models (see [BPMN Tutorial](https://camunda.com/bpmn/) and [Real-Life BPMN](https://www.amazon.com/Real-Life-BPMN-4th-introduction-DMN/dp/1086302095/) on details for modeling levels) - especially when they have a focus on *responsibilities and their borders*.

* *Technical/executable* models with a focus on *human work-flow* and its ongoing "ping pong" between several participants.

For these cases, also consider alternative methods to maintain and show roles:

* As a *visible part* of the *task name*, e.g. in between squared brackets []: *"Review tweet [Boss]"*. 

:::caution Camunda Platform 7 Only
During execution you can remove this part of the task name if you like by using simple mechanisms like shown in the [Task Name Beautifier](https://github.com/camunda/camunda-consulting/tree/master/snippets/task-name-beautifier) so it does not clutter your tasklist.
:::

* As a *text annotation* or a *custom artifact*

:::note
Roles are part of your executable BPMN process model as *technical attributes* anyway - even if hidden in the BPMN diagram. For example, they can be used during execution for assignment at runtime.
:::

## Helpful practices

### Emphasizing the happy path

You may want to emphasize the *"happy path"* leading to the delivery of a successful process result by placing the tasks, events, and gateways belonging to the happy path on a straight sequence flow in the center of your diagram - at least as often as possible.

<div bpmn="best-practices/creating-readable-process-models-assets/TwitterDemoProcess.bpmn" callouts="start_event_new_tweet,user_task_review_tweet,gateway_approved,service_task_publish_on_twitter,end_event_tweet_published" />

The *five* BPMN symbols belonging to the happy path are put on a straight sequence flow in the center of the diagram.


### Avoid modeling retry behavior

A common idea is to model retry behavior into your process models. This *should be avoided* in general. The following process model shows a typical example of this anti pattern:

<div bpmn="best-practices/creating-readable-process-models-assets/retry-anti-pattern.bpmn" thumbs="down" />

All operations use cases put into the model can be handled via Camunda tooling, e.g. by [retrying](/docs/components/concepts/job-workers/#completing-or-failing-jobs) or [Camunda Operate](/docs/components/operate/index/).


### Using collaboration diagrams

If you model on an operational level (see [BPMN Tutorial](https://camunda.com/bpmn/) and [Real-Life BPMN](https://www.amazon.com/Real-Life-BPMN-4th-introduction-DMN/dp/1086302095/) on details for modeling levels) use *collaboration diagrams* with several *separate pools* for the process participants [instead of lanes](#avoiding-lanes) as operational models using lanes make it very hard for the individual process participant to identify the details of their process involvement.

Furthermore, model just *one coherent process per pool* (apart from event subprocesses, of course), even though BPMN in principle allows several processes per pool. This improves readability by constituting a clear visual border around every process and by providing a natural space for labeling that part of the end-to-end process in the pool's header.

<div bpmn="best-practices/creating-readable-process-models-assets/using-collaboration-diagrams.bpmn" callouts="pool-invoice-collection,pool-invoice-approval,pool-invoice-payment" />

<span className="callout">1</span> 

The Team Assistance is responsible for initial "Invoice Collection" as well as "Invoice Clarification" - if applicable. Those two processes are modeled by using two separate pools for the team assistance, just as...

<span className="callout">2</span>

...the approver can see the "Invoice Approval" process in a separate pool and...

<span className="callout">3</span>

...the managing director can see the "Invoice Payment" process in a separate pool while the collaboration diagram as a whole shows the business analyst that the overall end-to-end process works.

Using *collaboration diagrams* with *separate pools* for the process participants allows to explicitly show interaction and communication between them by means of message flow and further improves readability by transparently showing the participants their own involvement in the end-to-end-process. As a consequence, they do not need to fully read and understand the end-to-end process in order to read, understand, and agree to their own involvement by looking at their own pools.

### Showing interaction with systems

Consciously decide how you want to model systems the process participants are interacting with. Use *data stores* to show systems which primarily serve as a means to store and retrieve data. Use - depending on your needs *collapsed* or *expanded* - *pools* for systems which are carrying out crucial activities in the process going way beyond storing and retrieving data.

<div bpmn="best-practices/creating-readable-process-models-assets/showing-interaction-with-systems.bpmn" callouts="pool-payments-processing,datastore-archived-pdfs,datastore-invoices-to-be-paid" />

<span className="callout">1</span>

A *collapsed pool* is used to represent a system which supports the process and/or carries out process tasks on its own. The pool could be expanded later to model the internal system details, maybe even with the goal to execute a technical process flow directly with a BPMN capable process engine.

<span className="callout">2</span>

A *data store* is used to represent a technical container meant to archive PDFs and store them for later retrieval.

<span className="callout">3</span>

Another *data store* is used to represent a container which could be a physical storage place for paper invoices to be paid at the moment but could become a representation for business objects in a database with the object state "to be paid" in the future.

When *choosing* between those *two options* for modeling systems (data stores, collapsed pools) keep in mind that only pools represent processes and therefore have the capability to be expanded and modeled in all their internal details later on.

### Avoiding excessive usage of data objects

Avoid excessive use of *data objects*, but use them cautiously to show the *most important data related aspects* of your process.

Experience shows that many data objects and especially many data associations quickly clutter your process model and that visual noise reduces readability - especially for less experienced readers.

You might find three practices helpful to find your own "right" amount of data visualization:

<div bpmn="best-practices/creating-readable-process-models-assets/avoiding-excessive-usage-of-data-objects.bpmn" callouts="data-object,data-store,message-flow" />

<span className="callout">1</span>

Cautiously use data objects and associations to show the *most important data related aspects* of your process. We could have modeled that all the tasks in the "Payments Creation" process either read, update, or delete the "new payment", however we decided that we just want to point out that the process works on a new payment object.

<span className="callout">2</span>

Use data stores for *coupling processes via data*. We could have modeled a lot of other tasks in the process that either read or update the "payments", however, we decided to just point out the most important aspect for the process diagram, which is that the "Payments Creation" process of delivery service is loosely coupled with the "Payments Processing" via commonly shared data.

<span className="callout">3</span>

Use message symbols to add *information regarding the message payload*, but only if this adds something useful to the model. Here we decided that it's helpful to know that this message does not only inform an adjustment possibility was checked, but that it also delivers all the necessary details of the adjustment.

### Avoiding changes to symbol size and color 

Leave the *size of symbols as it is* by default. For example, different sizes of tasks or events suggest that the bigger symbol is more important than the smaller one - an often unwarranted assumption. Instead of writing long labels, use short and consistent labels in line with your [naming conventions](naming-bpmn-elements.md) and move all additional information into BPMN annotations associated to your specific BPMN element.

Furthermore, avoid *excessive use of colors*. Experience shows that colors are visually very strong instruments and psychologically very suggestive, but will typically suggest different things to different readers. Additionally, a colorful model often looks less professional.

However, there are valid exceptions. For example, you could mark the *happy path* through a process with a visually weak coloring:

<div bpmn="best-practices/creating-readable-process-models-assets/TwitterDemoProcessColor.bpmn" />

Another case for useful coloring might be to make a visual difference between *human* and *technical flows* within a bigger collaboration diagram by coloring the header bar on the left side of the pools.