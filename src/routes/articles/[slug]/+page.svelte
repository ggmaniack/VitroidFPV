<script>
	import { page } from '$app/stores';
	import BuildProduct from "$components/buildsPage/buildProduct.svelte";
	import MainHeader from "$components/mainHeader.svelte";
	import Header from "$components/Header.svelte";
	import Paragraph from "$components/Paragraph.svelte";

	import { marked } from 'marked';
	import { onMount } from 'svelte';
	import { fade } from 'svelte/transition';

	const modules = import.meta.glob("/modules/articles/*.md", {eager: true});
	// console.log(JSON.stringify(modules, null, 2));

	let slug = $page.params.slug
	// console.log(slug)
	let slugModule = modules[`/modules/articles/${slug}.md`]

	let categoryColor
	let hexColor
	switch (slugModule.metadata.category) {
		case "Guides":
			categoryColor = "green"
			hexColor = "#87cc52"
			break
		case "Tutorials":
			categoryColor = "cyan"
			hexColor = "#5ad8e6"
			break
		case "News":
			categoryColor = "red"
			hexColor = "#d6395b"
			break
		case "Reviews":
			categoryColor = "yellow"
			hexColor = "#ffcc00"
			break
		case "Misc":
			categoryColor = "violet"
			hexColor = "#9550ba"
			break
		default:
			categoryColor = "violet"
			hexColor = "#9550ba"
	}
	let postedDate = new Date(slugModule.metadata.date)
	let postedDateFormatted = postedDate.toLocaleDateString("en-US", {year: "numeric", month: "long", day: "numeric"})
	// console.log(postedDateFormatted)

	const headerRegex = /(?<flag>#{1,6})\s+(?<content>.+)/g
	let headers = 
		Array
		.from(slugModule.metadata.content.matchAll(headerRegex))
		.map(({ groups: { flag, content } }) => ({
			heading: `${ flag.length }`,
			content,
		}))
	console.log(headers)

	let intersecionOptions = {
		root: null,
		rootMargin: "0px 0px -200px 0px",
		threshold: 1
	}
	let intersectingHeader = ""
	let	callback = (entries, observer) => {
		entries.forEach(entry => {
			if (entry.isIntersecting) {
				// console.log(entry.target.innerHTML)
				intersectingHeader = entry.target.innerHTML
			}
		})
	}
	onMount(() => {
		let intersectingTargets = document.querySelectorAll(".article.md h1, .article.md h2, .article.md h3, .article.md h4")
		let observer = new IntersectionObserver(callback, intersecionOptions);
		intersectingTargets.forEach(target => {
			observer.observe(target)
		})

		if (window.location.hash) {
			setTimeout(() => {
				const el = document.getElementById(window.location.hash.slice(1));
				if (el) {
					el.scrollIntoView();
				}
			}, 300);
		}
	})
	
	let prefix = slugModule.metadata.category;
	let titleRaw = slugModule.metadata.title;
	let title = " - " + titleRaw;
	let color = hexColor;
	let imgRaw = slugModule.metadata.img;
	let img = ""
	// if imgRaw is relative, add "https://vitroidfpv-sv.netlify.app" to the beginning
	if (imgRaw.startsWith("/")) {
		img = "https://vitroidfpv-sv.netlify.app" + imgRaw
	}
	let description = slugModule.metadata.description;
	let category = slugModule.metadata.category;

	let imgOg = `https://vitroidfpv-sv.netlify.app/og?title=${titleRaw}&description=${description}&category=${category}&img=${img}`
</script>

<svelte:head>
	<title>VitroidFPV{title}</title>
	<meta name="author" content="VitroidFPV" />
	<meta property="og:image" content="{imgOg}" />
	<meta name="twitter:card" content="summary_large_image">
	<meta property="og:type" content="website" />
	<meta property="og:site_name" content="VitroidFPV" />
	<meta property="article:author" content="VitroidFPV" />
	<meta property="og:title" content="{prefix}{title}" />
	<meta name="description" content={description} />
	<meta property="og:description" content={description} />
	<meta content="https://vitroidfpv.com/" property="og:url" />
	<meta name="theme-color" content={color} />
</svelte:head>


<div class="flex flex-col mr-12 md:visible invisible sticky top-0 -mt-[34rem] left-full w-fit">
	<div class="text-highlight dark:text-highlight-dark text-3xl font-semibold mt-8 mb-4 border-b-2 border-current">Contents:</div>
	{#each headers as header}
		{#if header.heading < 5}
		<a 	href="#{header.content.replaceAll(" ", "-").toLowerCase()}" 
		class:intersecting-header={header.content === intersectingHeader} 
		class:headeer={header.content != intersectingHeader}
		class="text-base mb-3 ml-{header.heading} contents-{header.heading} hover:translate-x-1 hover:text-highlight dark:hover:text-highlight-dark duration-300">{header.content}</a>
		{/if}
	{/each}
</div>
<div class="content-box">
	<!-- <img src={slugModule.metadata.img} alt="" class="w-3/4" /> -->
	<div class="flex flex-col pr-0 article-card {slugModule.metadata.category} rounded-2xl mb-8 w-fit md:mr-72">
		<div class="rounded-2xl {slugModule.metadata.category} relative select-none group overflow-hidden">
			<div class="absolute card-gradient {slugModule.metadata.category} w-full h-full rounded-2xl opacity-100 group-hover:scale-x-0 origin-left group-hover:blur-sm blur-none duration-500 z-10 backdrop-saturate-0 hover:backdrop-saturate-100"></div>
			<img src="{slugModule.metadata.img}" alt="" class="w-[64rem] m-0 h-96 object-cover rounded-2xl duration-500">
		</div>
	</div>
	<div class="flex-col items-start border-b-[1px] border-gray-700 mb-8 md:mr-72">
		<h1 class="text-{categoryColor} text-5xl mb-2">{slugModule.metadata.title}</h1>
		<h2 class="text-3xl">{slugModule.metadata.description}</h2>
		<div class="my-4">Posted on <span class="text-{categoryColor}">{postedDateFormatted}</span> by <span class="text-{categoryColor}">{slugModule.metadata.author}</span></div>
	</div>

	<div class="flex md:pr-72">
		<div class="article md">
			{@html marked.parse(slugModule.metadata.content)}
		</div>
	</div>
</div>

<style>
	.header {
		color: currentColor;
		/* transform: translateX(0); */
	}

	.intersecting-header {
		color: var(--range-handle);
		/* transform: translateX(8px); */
	}

	.contents-1 {
		font-size: 1.25rem;
	}
	.contents-2 {
		font-size: 1rem;
	}
	.contents-3 {
		font-size: 0.75rem;
	}
</style>