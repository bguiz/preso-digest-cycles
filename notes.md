We will be taking a very concise look into digest cycles.

This is a diagram from the official AngularJS documentation that explains what a digest cycle is. If this goes over the top of your head, don't worry, it did for me too. It is more complicated than it needs to be.

A digest cycle is, quite simply put, code that runs at an interval. Now this interval may be sometimes simply as fast as possible after the previous one, and sometimes the interval is set.

They are typically used to aggregate expensive tasks such that they do not have to run multiple times when there is no need to do so. For example, DOM rendering.

While we are here to talk about digest cycles in the context of Javascript SPAs, a discussion of this will not be complete without discussing where the idea for this originated from.

The Game Loop is a term familiar to most games developers. When writing a game one would typically set the interval of the game loop, such that each cycle runs as soon as possible after the previous opne completes. As such a variable frame rate is inevitable, and techniques such as linear interploation of time elapsed is used to calculate the rendered position of a moving object, for example. I've tweeted the link to the presentation, so you can browse these articles I have linked afterward.

In single page apps, digest cycles are also used. Their intent here is quite different, because maxing out frames per second is not usually a goal for web apps. When using dirty checking to observe for model state changes, SPAs have taken two different approaches - dirty checking and accessors, which you can read about on my blog. In fact this presentation is a derivative work of that post.

When using dirty checking, as AngularJS does, a digest cycle is necessary. When using the alternative, accessors, as BackboneJS and EmberJS do, a digest cycle is not necessary. In fact BackboneJS does not have one, but EmberJS does.

So why are digest cycles important in SPA frameworks? Firstly, as mentioned earlier, it is where you would put your code that dirty checks your models for differences to previous state. Secondly - and this is why EmberJS uses it despite using accessors - is that it improves efficiency.

Each change on each model setting off a DOM update, and thus a re-render, which most often triggers a reflow. Instead of doing this we aggrgate the changes until the point where the digest cycle executes; then update a purely in-memory buffer with the new DOM with all the changes, which then re-renders and reflows, just once per digest cycle.

Changes listeners that are triggered are aggregated in a similar manner, and all of them are fired at once during the digest cycle. 

Computed properties rely on the framework maintaining a directed acyclic graph (per model), and these are expensive to traverse. So we defer traversals to - you guessed it - once per digest cycle.

Here is an example of a computed property in an Ember app. What this says is "whenever the weight or height properties of a person change, the bmi property could change too."

Now that we know more about digest cycles - what is the main takeaway from it? The number one thing to avoid, is doing too much in each digest cycle. Once the time taken to perform the work needed in each digest cycle approaches or exceeds the duration of the interval between each one, the app will become noticeably laggy.

Here I have linked Misko Hevery's Stackoverflow post on dirty checking, where he works out some numbers for what these thresholds are. I have also got this snippet by Brian ford on throttling to esnure that changes do not happen as often.
