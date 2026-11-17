<script lang="ts">
	import { enhance } from '$app/forms';
	import { onMount } from 'svelte';
	import { projects, profile } from '$lib/data';
	import ProjectRow from '$lib/components/ProjectRow.svelte';

	type Theme = 'light' | 'dark';
	type Status = 'idle' | 'loading' | 'success' | 'error';

	// ——— Ganti dengan data aslimu ———
	const EMAIL = 'azkanajmatusyakura8@gmail.com';
	const GITHUB = 'https://github.com/azkanajmatusyakura8';
	const MAX_PESAN = 500;

	const links = [
		{ href: '#proyek', label: 'Proyek', id: 'proyek' },
		{ href: '#tentang', label: 'Tentang', id: 'tentang' },
		{ href: '#kontak', label: 'Kontak', id: 'kontak' }
	];

	let theme: Theme = $state('light');
	let status: Status = $state('idle');
	let errorMessage = $state('');
	let menuOpen = $state(false);
	let active = $state('');
	let pesan = $state('');
	let scrolled = $state(false);

	// tema: ikuti preferensi sistem, lalu ingat pilihan pengguna
	onMount(() => {
		try {
			const saved = localStorage.getItem('theme') as Theme | null;
			theme =
				saved ?? (matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light');
		} catch {
			/* storage tidak tersedia */
		}

		const onScroll = () => (scrolled = window.scrollY > 8);
		onScroll();
		window.addEventListener('scroll', onScroll, { passive: true });

		// tandai menu yang sedang aktif
		const io = new IntersectionObserver(
			(entries) => {
				for (const e of entries) if (e.isIntersecting) active = e.target.id;
			},
			{ rootMargin: '-45% 0px -50% 0px' }
		);
		links.forEach((l) => {
			const el = document.getElementById(l.id);
			if (el) io.observe(el);
		});

		return () => {
			window.removeEventListener('scroll', onScroll);
			io.disconnect();
		};
	});

	function toggleTheme() {
		theme = theme === 'dark' ? 'light' : 'dark';
		try {
			localStorage.setItem('theme', theme);
		} catch {
			/* abaikan */
		}
	}

	// animasi muncul saat di-scroll (hormati prefers-reduced-motion)
	function reveal(node: HTMLElement, delay = 0) {
		if (typeof matchMedia === 'undefined' || matchMedia('(prefers-reduced-motion: reduce)').matches)
			return;
		node.style.opacity = '0';
		node.style.transform = 'translateY(18px)';
		node.style.transition = `opacity .7s ease ${delay}ms, transform .7s ease ${delay}ms`;
		const io = new IntersectionObserver(
			([e]) => {
				if (e.isIntersecting) {
					node.style.opacity = '1';
					node.style.transform = 'none';
					io.disconnect();
				}
			},
			{ threshold: 0.12 }
		);
		io.observe(node);
		return { destroy: () => io.disconnect() };
	}

	const field =
		'h-12 w-full border border-line bg-bg px-3.5 text-[15px] outline-none transition-colors placeholder:text-muted hover:border-ink/40 focus:border-primary focus:ring-1 focus:ring-primary';
	const label = 'font-mono text-[11px] tracking-[1.1px] text-muted';
	const eyebrow = 'font-mono text-[11px] tracking-[1.1px]';
</script>

<svelte:head>
	<title>Azka Najmatusyakura — Portofolio Web Developer</title>
	<meta
		name="description"
		content="Azka Najmatusyakura, siswa PPLG SMK Negeri 8 Semarang. Membangun antarmuka web yang cepat, rapi, dan mudah dipakai."
	/>
	<meta name="theme-color" content={theme === 'dark' ? '#0b0b0c' : '#ffffff'} />
	<meta property="og:title" content="Azka Najmatusyakura — Portofolio" />
	<meta property="og:description" content="Siswa PPLG SMK Negeri 8 Semarang. Web developer." />
	{@html `<style>html{scroll-behavior:smooth;scroll-padding-top:76px}@media (prefers-reduced-motion:reduce){html{scroll-behavior:auto}}</style>`}
</svelte:head>

<div class="min-h-dvh bg-bg text-ink transition-colors duration-300 {theme === 'dark' ? 'dark' : ''}">
	<a
		href="#proyek"
		class="sr-only focus:not-sr-only focus:fixed focus:top-3 focus:left-3 focus:z-50 focus:bg-primary focus:px-4 focus:py-2 focus:text-on-primary"
	>
		Lewati ke konten
	</a>

	<!-- nav -->
	<header
		class="sticky top-0 z-40 border-b bg-bg/85 backdrop-blur transition-colors {scrolled
			? 'border-line'
			: 'border-transparent'}"
	>
		<div
			class="mx-auto flex h-[60px] max-w-[1440px] items-center justify-between px-6 lg:h-[72px] lg:px-24"
		>
			<a href="/" class="text-[15px] font-semibold tracking-tight lg:text-base">
				azka<span class="text-primary">.</span>najma
			</a>

			<div class="flex items-center gap-2 lg:gap-8">
				<nav class="hidden items-center gap-7 lg:flex" aria-label="Navigasi utama">
					{#each links as l}
						<a
							href={l.href}
							aria-current={active === l.id ? 'true' : undefined}
							class="relative text-sm transition-colors hover:text-ink {active === l.id
								? 'text-ink'
								: 'text-muted'}"
						>
							{l.label}
							<span
								class="absolute -bottom-1.5 left-0 h-px bg-primary transition-all duration-300 {active ===
								l.id
									? 'w-full'
									: 'w-0'}"
							></span>
						</a>
					{/each}
				</nav>

				<button
					type="button"
					aria-label="Ganti tema"
					aria-pressed={theme === 'dark'}
					onclick={toggleTheme}
					class="flex h-11 w-11 items-center justify-center border border-line transition-colors hover:bg-surface focus-visible:outline-2 focus-visible:outline-primary"
				>
					<svg
						width="17"
						height="17"
						viewBox="0 0 24 24"
						fill="none"
						stroke="currentColor"
						stroke-width="1.5"
						stroke-linecap="round"
						stroke-linejoin="round"
						class="dark:hidden"
					>
						<path d="M21 12.8A9 9 0 1 1 11.2 3a7 7 0 0 0 9.8 9.8Z" />
					</svg>
					<svg
						width="17"
						height="17"
						viewBox="0 0 24 24"
						fill="none"
						stroke="currentColor"
						stroke-width="1.5"
						stroke-linecap="round"
						class="hidden dark:block"
					>
						<circle cx="12" cy="12" r="4" />
						<path
							d="M12 2v2M12 20v2M4.9 4.9l1.4 1.4M17.7 17.7l1.4 1.4M2 12h2M20 12h2M4.9 19.1l1.4-1.4M17.7 6.3l1.4-1.4"
						/>
					</svg>
				</button>

				<button
					type="button"
					aria-label={menuOpen ? 'Tutup menu' : 'Buka menu'}
					aria-expanded={menuOpen}
					aria-controls="menu-mobile"
					onclick={() => (menuOpen = !menuOpen)}
					class="flex h-11 w-11 items-center justify-center border border-line transition-colors hover:bg-surface focus-visible:outline-2 focus-visible:outline-primary lg:hidden"
				>
					<svg
						width="18"
						height="18"
						viewBox="0 0 24 24"
						fill="none"
						stroke="currentColor"
						stroke-width="1.5"
						stroke-linecap="round"
					>
						{#if menuOpen}
							<path d="M6 6l12 12M18 6 6 18" />
						{:else}
							<path d="M4 7h16M4 12h16M4 17h16" />
						{/if}
					</svg>
				</button>
			</div>
		</div>

		{#if menuOpen}
			<nav
				id="menu-mobile"
				aria-label="Menu mobile"
				class="border-t border-line bg-bg px-6 py-2 lg:hidden"
			>
				{#each links as l}
					<a
						href={l.href}
						onclick={() => (menuOpen = false)}
						class="flex h-12 items-center justify-between border-b border-line text-[15px] last:border-b-0 {active ===
						l.id
							? 'text-ink'
							: 'text-muted'}"
					>
						{l.label}
						<span class="font-mono text-[11px] tracking-[1.1px]">→</span>
					</a>
				{/each}
			</nav>
		{/if}
	</header>

	<main>
		<!-- hero -->
		<section class="mx-auto max-w-[1440px] px-6 pt-14 pb-14 lg:px-24 lg:pt-[96px]">
			<p class="{eyebrow} mb-6 text-muted" use:reveal={0}>
				PORTOFOLIO / PPLG / SMKN 8 SEMARANG
			</p>

			<h1
				class="text-[40px] leading-[0.92] font-medium tracking-[-1.2px] sm:text-[64px] sm:tracking-[-2px] lg:text-[148px] lg:tracking-[-6px]"
				use:reveal={60}
			>
				Azka<br />
				<span class="text-primary">Najmatusyakura</span>
			</h1>

			<div class="mt-9 flex flex-col gap-6" use:reveal={160}>
				<p class="max-w-[600px] text-[17px] leading-[1.6] text-muted lg:text-xl">
					Siswa PPLG di SMK Negeri 8 Semarang. Membangun antarmuka web yang cepat, rapi, dan mudah
					dipakai.
				</p>

				<div class="flex flex-wrap items-center gap-3">
					<a
						href="#proyek"
						class="group flex h-12 items-center gap-2 bg-primary px-6 text-[15px] font-medium text-on-primary transition-opacity hover:opacity-90 focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-primary"
					>
						Lihat proyek
						<span class="transition-transform group-hover:translate-y-0.5" aria-hidden="true">↓</span>
					</a>
					<a
						href="#kontak"
						class="flex h-12 items-center border border-line px-6 text-[15px] text-ink transition-colors hover:bg-surface focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-primary"
					>
						Hubungi saya
					</a>
				</div>

				<p class="{eyebrow} flex items-center gap-2 text-muted">
					<span class="relative flex h-2 w-2" aria-hidden="true">
						<span
							class="absolute inline-flex h-full w-full animate-ping rounded-full bg-success opacity-60"
						></span>
						<span class="relative inline-flex h-2 w-2 rounded-full bg-success"></span>
					</span>
					TERSEDIA UNTUK PROYEK BARU — 2026
				</p>
			</div>
		</section>

		<!-- band -->
		<div class="border-t border-line">
			<div class="overflow-hidden">
				<img
					src={theme === 'dark' ? '/images/Mind Lattice.jpg' : '/images/Mind Lattice.jpg'}
					alt="Suasana kegiatan di Lab PPLG SMK Negeri 8 Semarang"
					loading="lazy"
					class="h-[220px] w-full object-cover transition-transform duration-700 hover:scale-[1.02] lg:h-[420px]"
				/>
			</div>
			<div class="mx-auto max-w-[1440px] px-6 py-4 lg:px-24">
				<p class="{eyebrow} text-muted">LAB PPLG — SMK NEGERI 8 SEMARANG · 2026</p>
			</div>
		</div>

		<!-- proyek -->
		<section id="proyek" class="border-t border-line">
			<div class="mx-auto max-w-[1440px] px-6 pt-5 pb-14 lg:px-24 lg:pb-24">
				<div class="flex items-baseline justify-between">
					<p class={eyebrow}>01 — PROYEK</p>
					<p class="{eyebrow} text-muted">{String(projects.length).padStart(2, '0')} TERPILIH</p>
				</div>

				<div class="mt-7 flex flex-col divide-y divide-line lg:mt-10">
					{#each projects as project, i (project.title)}
						<div use:reveal={i * 60}>
							<ProjectRow {project} index={i} />
						</div>
					{/each}
				</div>
			</div>
		</section>

		<!-- tentang -->
		<section id="tentang" class="border-t border-line">
			<div class="mx-auto max-w-[1440px] px-6 pt-5 pb-14 lg:px-24 lg:pb-24">
				<p class={eyebrow}>02 — TENTANG</p>

				<div class="mt-7 flex flex-col items-start gap-8 lg:mt-10 lg:flex-row lg:gap-24">
					<div class="w-full overflow-hidden lg:w-[420px] lg:shrink-0" use:reveal={0}>
						<img
							src={theme === 'dark' ? '/images/loopy.jpg' : '/images/loopy.jpg'}
							alt="Foto Azka Najmatusyakura"
							loading="lazy"
							class="h-[428px] w-full object-cover lg:h-[525px]"
						/>
					</div>

					<div class="flex w-full flex-col gap-7" use:reveal={100}>
						<p class="max-w-[520px] text-[17px] leading-[1.6] text-muted lg:text-xl">
							{profile.bio}
						</p>

						<div class="flex flex-col divide-y divide-line border-y border-line">
							{#each profile.facts as fact}
								<div class="flex items-center justify-between gap-8 py-4">
									<span class="{label}">{fact.label}</span>
									<span class="text-right text-[15px]">{fact.value}</span>
								</div>
							{/each}
						</div>

						<div class="flex flex-col gap-3">
							<span class={label}>KEAHLIAN</span>
							<ul class="flex flex-wrap gap-2">
								{#each profile.skills as skill}
									<li
										class="border border-line px-2.5 py-[7px] font-mono text-[11px] tracking-[1.1px] text-muted transition-colors hover:border-primary hover:text-ink"
									>
										{skill}
									</li>
								{/each}
							</ul>
						</div>
					</div>
				</div>
			</div>
		</section>

		<!-- kontak -->
		<section id="kontak" class="border-t border-line">
			<div class="mx-auto max-w-[1440px] px-6 pt-5 pb-14 lg:px-24 lg:pb-24">
				<p class={eyebrow}>03 — KONTAK</p>

				<div class="mt-7 flex flex-col gap-8 lg:mt-10 lg:flex-row lg:gap-24">
					<div class="flex max-w-[420px] flex-col gap-5" use:reveal={0}>
						<p class="text-[17px] leading-[1.6] text-muted lg:text-xl">
							Punya pertanyaan atau ingin bekerja sama? Kirim pesan, saya balas secepatnya.
						</p>
						<a
							href="mailto:{EMAIL}"
							class="w-fit border-b border-primary pb-0.5 font-mono text-xs tracking-[1.1px] text-primary"
						>
							{EMAIL}
						</a>
					</div>

					<form
						method="POST"
						action="?/kirim"
						use:enhance={() => {
							status = 'loading';
							errorMessage = '';
							return async ({ result, update }) => {
								await update({ reset: result.type === 'success' });
								if (result.type === 'success') {
									status = 'success';
									pesan = '';
									setTimeout(() => {
										if (status === 'success') status = 'idle';
									}, 6000);
								} else {
									status = 'error';
									errorMessage =
										result.type === 'failure' && typeof result.data?.message === 'string'
											? result.data.message
											: 'Gagal mengirim. Coba lagi.';
								}
							};
						}}
						class="flex w-full flex-col gap-4 lg:w-[440px] lg:shrink-0"
						use:reveal={100}
					>
						<label class="flex flex-col gap-2">
							<span class={label}>NAMA</span>
							<input
								name="nama"
								required
								minlength="2"
								autocomplete="name"
								placeholder="Nama lengkap kamu"
								class={field}
							/>
						</label>

						<label class="flex flex-col gap-2">
							<span class={label}>EMAIL</span>
							<input
								name="email"
								type="email"
								required
								autocomplete="email"
								inputmode="email"
								placeholder="nama@email.com"
								class={field}
							/>
						</label>

						<label class="flex flex-col gap-2">
							<span class="flex items-center justify-between">
								<span class={label}>PESAN</span>
								<span class="{label} tabular-nums">{pesan.length}/{MAX_PESAN}</span>
							</span>
							<textarea
								name="pesan"
								required
								rows="5"
								maxlength={MAX_PESAN}
								bind:value={pesan}
								placeholder="Tulis pesan kamu di sini"
								class="min-h-[120px] w-full resize-y border border-line bg-bg px-3.5 py-3 text-[15px] outline-none transition-colors placeholder:text-muted hover:border-ink/40 focus:border-primary focus:ring-1 focus:ring-primary"
							></textarea>
						</label>

						<button
							type="submit"
							disabled={status === 'loading'}
							class="flex h-12 items-center justify-center gap-2 border px-6 text-[15px] font-medium transition-opacity focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-primary {status ===
							'loading'
								? 'cursor-not-allowed border-line bg-surface text-muted'
								: 'border-transparent bg-primary text-on-primary hover:opacity-90'}"
						>
							{#if status === 'loading'}
								<svg
									width="16"
									height="16"
									viewBox="0 0 24 24"
									fill="none"
									stroke="currentColor"
									stroke-width="1.5"
									stroke-linecap="round"
									class="animate-spin"
									aria-hidden="true"
								>
									<path d="M12 3a9 9 0 1 0 9 9" />
								</svg>
								Mengirim...
							{:else}
								Kirim pesan
							{/if}
						</button>

						<!-- satu-satunya area status (tidak dobel dengan tombol) -->
						<div aria-live="polite" role="status">
							{#if status === 'success'}
								<div class="flex items-center gap-3 border border-success/40 px-4 py-3.5">
									<svg
										width="16"
										height="16"
										viewBox="0 0 24 24"
										fill="none"
										stroke="currentColor"
										stroke-width="1.5"
										stroke-linecap="round"
										stroke-linejoin="round"
										class="shrink-0 text-success"
										aria-hidden="true"
									>
										<path d="M20 6 9 17l-5-5" />
									</svg>
									<span class="text-sm text-success">Pesan terkirim. Terima kasih!</span>
								</div>
							{:else if status === 'error'}
								<div class="flex items-center gap-3 border border-danger/40 px-4 py-3.5">
									<svg
										width="16"
										height="16"
										viewBox="0 0 24 24"
										fill="none"
										stroke="currentColor"
										stroke-width="1.5"
										stroke-linecap="round"
										stroke-linejoin="round"
										class="shrink-0 text-danger"
										aria-hidden="true"
									>
										<circle cx="12" cy="12" r="9" />
										<path d="M12 8v5M12 16.5v.01" />
									</svg>
									<span class="text-sm text-danger">{errorMessage}</span>
								</div>
							{/if}
						</div>
					</form>
				</div>
			</div>
		</section>
	</main>

	<!-- footer -->
	<footer class="border-t border-line">
		<div class="mx-auto flex max-w-[1440px] flex-col gap-10 px-6 py-14 lg:px-24">
			<p
				class="max-w-[640px] text-[34px] leading-[1.05] font-medium tracking-[-0.6px] lg:text-[48px] lg:tracking-[-1.2px]"
				use:reveal={0}
			>
				Mari membangun <span class="text-primary">sesuatu.</span>
			</p>

			<div
				class="flex flex-col items-start gap-4 lg:flex-row lg:items-center lg:justify-between lg:gap-6"
			>
				<a href="mailto:{EMAIL}" class="font-mono text-xs tracking-[1.1px] text-primary">
					{EMAIL}
				</a>
				<div class="flex items-center gap-6">
					<a href="#proyek" class="{eyebrow} text-muted transition-colors hover:text-ink">PROYEK</a>
					<a
						href={GITHUB}
						target="_blank"
						rel="noopener noreferrer"
						class="{eyebrow} text-muted transition-colors hover:text-ink">GITHUB</a
					>
					<a href="mailto:{EMAIL}" class="{eyebrow} text-muted transition-colors hover:text-ink">
						EMAIL
					</a>
				</div>
			</div>

			<div
				class="flex flex-col gap-2 border-t border-line pt-6 lg:flex-row lg:items-center lg:justify-between"
			>
				<p class="{eyebrow} text-muted">© 2026 AZKA NAJMATUSYAKURA</p>
				<p class="{eyebrow} text-muted">LAB PPLG · SMK NEGERI 8 SEMARANG</p>
			</div>
		</div>
	</footer>
</div>