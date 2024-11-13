<h2>Metazine: a newspaper-like RSS aggregator</h2>
<h4>November 13, 2024</h4>

<p>
	In this post I'd like to introduce a personal project of mine. The current working title is
	'Metazine' and very briefly described, it collects RSS feeds and presents them in an online
	newspaper style layout. The app is build on top of Ruby on Rails
</p>

<h3>Background to the idea</h3>

<p>
	I started working on this project during the Corona years. My intent was to build an app that acts
	as an editor of an online magazine, using the RSS and Atom feeds from any sources as the content.
</p>

<p>
	It would have curation based on the times an article was read and commented on, and this curation
	would be realized in the positions articles would be given in the visual hierarchy.
</p>

<p>
	Commenting would be implemented in a way that implements the ActivityPub (AP) protocol (Mastodon,
	Lemmy and others). Basically, the app would act as an AP server with a single user.
</p>

<p>
	There would be also automatic translation of articles, so that it'd be possible to collect stories
	from local authors around the world.
</p>

<p>
	And, it would be possible to read articles within the app, with a consistent, good to read, design
	between articles from different sources.
</p>

<h3>Forgetting about the project and eventual rewrite</h3>

<p>
	At around 2022 I stopped working on the project and eventually turned off the only instance. In
	early 2024 I decided to come back to the idea and rewrote the app with a later Rails version.
</p>

<p>
	Through this project, I've been exploring different hosting services, learning more about DNS and
	learning about the AP protocol. Currently there is some basic AP functionality: you can discover
	an instance on some platforms implementing AP, such as Mastodon. Why my instance is not showing up
	on every instance is still a mystery to me though. You can try finding my instance called at
	@editor@newfutu.re .
</p>

<style src="../../../app.css"></style>
