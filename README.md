# xSite
Proof of Concept in order to achieve Intention.

## Itention (the answer to “What?”)
1. **Meta-framework**: That isn’t “yet another framework”, which is going to do the same things as others in a different way. The opposite: it provides a way to re-use everything we already have at the moment in a standard manner. So you don’t have to choose either this project or - say - Symfony or Laravel or Yii or Drupal or Modx. Consider it as a meta-framework, which helps to deal with your resources uniformly, - and you decide when and what resources exactly you’re going to interact with.
2. **...with high-load in mind**: This project responses to the need of refactoring and even re-making the site once it reaches the scale of high-load. The whole architecture from routing to database interaction needs to be reviewed. And here the project comes: it is made with such simple thought in mind - let build small sites as big ones. Of course, Convention over configuration - so you don’t need to setup Redis in order to launch simple blog with it. But once you’re ready to grow to high-load - it should be ready without additional effort, seamlessly.
3. **...and no magic at all**: In order to ease the effort of developing, maintaining and debugging, every piece of code should be available via IDE autocompletion and code navigation - or at worst could be definitely found by code search. No ${$paramName} magic or similar is welcome.

## Concept (the answer to “How?”)
Micro-service framework.
That includes but is not limited to:

1. **Every data is a resource in terms of [REST](https://en.wikipedia.org/wiki/Representational_state_transfer)**: Why should we invent all those things again and again if we already have almost everything we need?
 - What if we consider every View in terms of MVC as a REST-resource - so (again) we could retrieve it from everywhere either by AJAX call or server-side during rendering of another view?
 - What if the same for Model?
 - What if we consider user session as a resource - then we could manipulate it with just POST, GET, PUT and DELETE from any part of our application either server-side or client-side?

 **\+ Reusage across several applications**: It drastically reduces duplication and increases reusage - as the same resources could be used by separate applications: a site, several mobile applications for different platforms (Android, iOS, Windows Mobile) and even desktop (again, no matter which OS is used: Windows, Mac or Linux-based).

 **\+ Increasing performance and user experience by taming cache invalidation**: It would definitely help to solve one of the hardest tasks in programming: cache invalidation - during PUT or DELETE we know what we just changed, so it wouldn’t be a great problem to invalidate cache only for particular resource. Other job would be done by [ETags of HTTP](https://en.wikipedia.org/wiki/HTTP_ETag). We are standing on titans’ shoulders - so why don’t we just re-use how HTTP deals with cache instead of manipulating it ourselves?

2. **Every piece of logic is a [microservice](https://en.wikipedia.org/wiki/Microservices), based on [finite state machine](https://en.wikipedia.org/wiki/Finite-state_machine), which is available via URL**:

**\+ Bug-free & error-free**: No more inconsistent behavior - as all of the logic (either controllers or models or views) is defined declaratively using state machines. It means that each of the application literally does what it's intended to do - not more and not less.

 **\+ New level of calculation caching**: Let's get rid of plenty types and ways of caching - let's unify it by relying on HTTP-caching!

 **\+ Ultimate scalability**: You could assign hosting resources up to per-function basis!

3. **Fully RESTfully, resource repository versioning**: There is [some buzz at StackOverflow about how to implement user authorization to REST server if it is not intended to store any state](http://stackoverflow.com/questions/3105296/if-rest-applications-are-supposed-to-be-stateless-how-do-you-manage-sessions). The [answer](http://stackoverflow.com/a/3105337) seems to be quite simple: “stateless” means every data about the state should come with the request. So either authorize during each request (which is not very comfortable, isn’t it?) or each request should have a parameter that represents a state. Yes, that could be a cookie - but what if we go further? What if we consider “a commit” as such state parameter? Yes, what if we have resource service versioning and state param just contains a revision of resource service?

 **\+ Total reproducibility by a single permanent link**: Each of requests has a reference to the state (commit) of the corresponding resource service. So (imagine that!) each of the requests is ultimately reproducable as it doesn’t make assumptions of the resource service state behind the scene. If some request leads to some error - it will lead to it, no matter who or when triggers the request again. No more “How to reproduce” section in bug-reports, no more fixtures in unit tests, just a concrete persistent (sic!) link to the site which fails (or does what it shouldn’t) - that’s it.

 **\+ Backup & restore as simple as `git push` & `git pull`**: Given each resource repository has its state, we could put it into the version control system (such as [git](https://en.wikipedia.org/wiki/Git)) - and then work with data as we used to do with source code! It includes forks, branches, pull-requests - everything was already created before for our convinience, why hesitate using it?

4. **“Matryoshka principle”, recursive nesting of Views and Models**: Imagine a View that retrieves another View during its render or Model that contains a sub-model (set of properties or composite property) - I guess, that’s not hard to imagine as they are the most common cases. So why don’t we stop multipliying entities beyond necessity? Is creating/learning all of those “chunks”, “blocks” and “templates” together with “template variables” better than just to say: “Ok, we have a View that could contain other Views”?

 **\+ Partial caching of dynamic content**: No more per-page caching! Given each View is a resource and if a resource has its own URL, we could invalidate cache for each View separately. It means that only changed parts of the page would trigger new HTTP-requests, the unchanged parts would be still got from browser cache.

5. **SELECT-transactions**: What if we put all of our SELECT-queries into one “transaction”-promise in order to do any additional processing we want with it? For example, optimize the quantity of the requests by retrieving consolidated data in order to achieve better performance?
