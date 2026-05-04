<svelte:head>
	<title>Contact – Rodrigo Gallardo</title>
</svelte:head>

<nav>
	<a href=".">Home</a>
	<a href="projects">Projects</a>
	<a class="current" href="contact">Contact</a>
	<a href="resume">Resume</a>
	<a href="https://github.com/rodrigoagallardo008" target="_blank">GitHub</a>
</nav>

<h1>Contact Information</h1>

<div class="contact-form">
	<form action="mailto:ragallar@mit.edu" method="GET">
		<label>
			Email:
			<input name="email" type="email">
		</label>

		<label>
			Subject:
			<input name="subject">
		</label>

		<label>
			Message:
			<textarea name="body"></textarea>
		</label>

		<button type="submit">Send</button>
	</form>
</div>
