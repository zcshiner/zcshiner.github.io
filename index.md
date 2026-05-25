---
layout: page
title: Home
---
<style type="text/css">
img.header-image {

}

main > div.wrapper {
	max-width: 100%;
	padding-left: 0;
	padding-right: 0;
}

main.page-content {
	padding-top: 0;
}

div.below-content {
	max-width: -webkit-calc(800px - (30px * 2));
	max-width: calc(800px - (30px * 2));
	margin-right: auto;
	margin-left: auto;
	padding-right: 30px;
	padding-left: 30px;
}
@media screen and (max-width: 800px) {
	div.below-content {
		max-width: -webkit-calc(800px - (30px));
		max-width: calc(800px - (30px));
		padding-right: 15px;
		padding-left: 15px;
	}
}

header.post-header {
	display:none;
}

div.profile-banner {
	display: grid;
	width: 100%;
	margin-bottom: 15px;
}

div.profile-banner > * {
	grid-area: 1 / 1;
}

img.header-image {
	width: 100%;
	display: block;
}

span.intro-text {
	align-self: center;
	justify-self: start;
	z-index: 100;
	font-size: clamp(1.2rem, 7vw, 12rem);
	color: white;
	font-weight: bold;
	padding: 0 1.5rem;
	line-height: 1.2em;
	width: min(9em, 90%);
}

</style>

<div class="profile-banner">
<img src="/images/z-home.jpg" class="header-image"/>
<span class="intro-text">My name is Zach and it's a pleasure to meet you.</span>
</div>

<div markdown=1 class="below-content">

## About Me

I am an experienced mechanical engineer that excels at giving fuzzy concepts harder edges and turning ideas into tangible products.

When I'm not at work I'm cycling or taking photos.  Hobbies are all about the gear, right?<br>
You can find me on [LinkedIn](https://www.linkedin.com/in/zcshiner/), [Instagram](https://www.instagram.com/zach.shiner/), and [Strava](https://www.strava.com/athletes/zshiner).
</div>
