<script>
	import BuildProduct from "$components/buildsPage/buildProduct.svelte";
	import MainHeader from "$components/mainHeader.svelte";
	import Header from "$components/Header.svelte";
	import Paragraph from "$components/Paragraph.svelte";

	const modules = import.meta.glob("/modules/buildLists/3inchCinewhoop/*.md", {eager: true});
	// console.log(modules)
	let grouped_modules = {};

	for (const k in modules) {
		const cat = modules[k].metadata.Category;
		if (grouped_modules[cat]) {
			grouped_modules[cat].push(modules[k]);
		} else {
			grouped_modules[cat] = [modules[k]];
		}
	}

	let prefix = "Builds";
	let titleRaw = "3\" Cinewhoop";
	let title = " - " + titleRaw;
	let color = "#ff9742";
	let img = "/uploads/images/cinewhoop_512.png";
	let description =
		"Meant to be used with a full size gopro, to carry it safely around hard, and soft targets alike, in close proximity. Wider, shorter motors, fully protected frames, and mini stacks or AIOs";
</script>

<svelte:head>
	<title>VitroidFPV{title}</title>
	<meta name="author" content="VitroidFPV" />
	<meta property="og:image" content="https://vitroidfpv-sv.netlify.app{img}" />
	<meta property="og:image:width" content="512" />
	<meta property="og:image:height" content="512" />
	<meta property="og:type" content="website" />
	<meta property="og:site_name" content="VitroidFPV" />
	<meta property="article:author" content="VitroidFPV" />
	<meta property="og:title" content="{prefix}{title}" />
	<meta name="description" content={description} />
	<meta property="og:description" content={description} />
	<meta content="https://vitroidfpv.com/" property="og:url" />
	<meta name="theme-color" content={color} />
</svelte:head>

<div class="content-box">
	<MainHeader>3" Cinewhoop</MainHeader>
	<Header text="Slow, safe, cinematic flying, indoors and outdoors" />
	<Paragraph>
		Meant to be used with a full size gopro, to carry it safely around hard, and soft targets alike, in close proximity. Wider, shorter motors, fully protected frames, and mini stacks or AIOs<br>
		This list is specced for 4/6s, and includes everything to build a full quad, except parts such as cameras, goggles, receiver and radio systems as those vary depending on prefference and you'll find recommendations on this site elsewhere.<br>
		The price range for each part is highlighted, so you can quickly find something to suit your budget needs
		<ul class="flex flex-row justify-between w-full flex-wrap mt-4">
			<li
				class="before:content-[''] before:pr-7 before:rounded-lg before:mr-2 before:bg-green before:relative mb-2"
			>
				Budget
			</li>
			<li
				class="before:content-[''] before:pr-7 before:rounded-lg before:mr-2 before:bg-orange before:relative mb-2"
			>
				Mid-range
			</li>
			<li
				class="before:content-[''] before:pr-7 before:rounded-lg before:mr-2 before:bg-red before:relative mb-2"
			>
				High-end
			</li>
			<li
				class="before:content-[''] before:pr-7 before:rounded-lg before:mr-2 before:bg-violet before:relative mb-2"
			>
				Highly varied
			</li>
		</ul>
	</Paragraph>

	<div class="flex flex-col">
		{#each Object.entries(grouped_modules) as [cat, contents]}
			<div class="{cat} my-8 w-full h-fit">
				<div
					class="text-4xl tracking-tight md:w-fit f-full px-1 md:ml-1 ml-2 cat {cat} mb-2 text-center"
					id={cat}
				>
					{cat}
				</div>
				<div
					class="ml-3 flex flex-col md:flex-row flex-wrap w-full md:justify-start md:items-start items-center border-b-[1px] border-white/10"
				>
					{#each contents as info}
						<BuildProduct
							color={info.metadata.color}
							title={info.metadata.title}
							price={info.metadata.price}
							point1={info.metadata.point1}
							point2={info.metadata.point2}
							point3={info.metadata.point3}
							point4={info.metadata.point4}
							point5={info.metadata.point5}
							text={info.metadata.text}
							link={info.metadata.link}
						/>
					{/each}
				</div>
			</div>
		{/each}
	</div>
</div>
