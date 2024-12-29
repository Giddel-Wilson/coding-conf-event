<script lang="ts">
	import { enhance } from '$app/forms';
	import { fade, fly } from 'svelte/transition';
	import { Upload } from 'lucide-svelte';
	import fullLogo from '$lib/images/logo-full.svg';
	import dbg from '$lib/images/background-desktop.png';
	import mbg from '$lib/images/background-mobile.png';
	import squigpt from '$lib/images/pattern-squiggly-line-top.svg';
	import squigptb from '$lib/images/pattern-squiggly-line-bottom.svg';
	import pls from '$lib/images/pattern-lines.svg';
	import plc from '$lib/images/pattern-circle.svg';
	import ptT from '$lib/images/pattern-ticket.svg';

	interface FormData {
		avatar: File | null;
		fullName: string;
		email: string;
		githubUsername: string;
	}

	interface FormErrors {
		avatar: string;
		fullName: string;
		email: string;
		githubUsername: string;
	}

	let formData: FormData = {
		avatar: null,
		fullName: '',
		email: '',
		githubUsername: ''
	};

	let errors: FormErrors = {
		avatar: '',
		fullName: '',
		email: '',
		githubUsername: ''
	};

	let avatarPreview = '';
	let isSubmitted = false;
	let ticketNumber = '';

	function validateForm(): boolean {
		let isValid = true;
		errors = {
			avatar: '',
			fullName: '',
			email: '',
			githubUsername: ''
		};

		if (!formData.fullName) {
			errors.fullName = 'Full name is required';
			isValid = false;
		}

		if (!formData.email) {
			errors.email = 'Email is required';
			isValid = false;
		} else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(formData.email)) {
			errors.email = 'Please enter a valid email address';
			isValid = false;
		}

		if (!formData.githubUsername) {
			errors.githubUsername = 'GitHub username is required';
			isValid = false;
		}

		if (!formData.avatar) {
			errors.avatar = 'Avatar is required';
			isValid = false;
		}

		return isValid;
	}

	function handleSubmit(event: SubmitEvent) {
		event.preventDefault();

		if (!validateForm()) return;

		isSubmitted = true;
		ticketNumber = Math.random().toString(36).substring(2, 8).toUpperCase();
	}

	function handleFileChange(event: Event) {
		const input = event.target as HTMLInputElement;
		const file = input.files?.[0];

		if (file) {
			if (file.size > 500000) {
				errors.avatar = 'File too large. Please upload a photo under 500KB.';
				return;
			}

			if (!['image/jpeg', 'image/png'].includes(file.type)) {
				errors.avatar = 'Please upload a JPG or PNG file.';
				return;
			}

			formData.avatar = file;
			avatarPreview = URL.createObjectURL(file);
			errors.avatar = '';
		}
	}

	function handleUploadClick() {
		document.getElementById('avatar')?.click();
	}
</script>

<div class="bg-gradient relative min-h-screen overflow-hidden pb-10 md:pb-0">
	<!-- Decorative elements -->
	<!-- background -->
	<div class="absolute left-0 top-0 h-full w-full">
		<img src={dbg} alt="Desktop background" class="hidden h-full w-full object-cover md:block" />
		<img src={mbg} alt="Mobile background" class="block h-full w-full object-cover md:hidden" />
	</div>

	<div class="absolute right-0 z-10 lg:top-20">
		<img src={squigpt} alt="Squiggly line" class="h-24 w-24 object-contain md:h-full md:w-full" />
	</div>

	<div class="absolute bottom-0 left-0 z-10">
		<img src={squigptb} alt="Squiggly line" class="h-full w-full object-contain" />
	</div>

	<div class="absolute top-0 z-0">
		<img src={pls} alt="pattern line" class="h-96 w-screen object-cover md:h-full" />
	</div>

	<div class="absolute -top-10 left-0 z-10 md:-top-28 md:left-28">
		<img src={plc} alt="pattern circle line" class="h-20 w-20 object-contain md:h-full md:w-full" />
	</div>

	<div class="absolute -right-16 top-1/2 z-10 md:right-1/4 md:top-96">
		<img src={plc} alt="pattern circle line" class="h-32 w-32 object-contain md:h-full md:w-full" />
	</div>

	<div
		class="absolute bottom-0 left-0 h-96 w-96 rounded-full bg-[#ff6b6b] opacity-5 blur-[128px]"
	></div>

	<div class="container relative z-10 mx-auto max-w-md px-4 py-8 md:max-w-2xl">
		{#if !isSubmitted}
			<div class="mb-10 text-center">
				<img src={fullLogo} alt="Coding Conf Logo" class="mx-auto mb-10 h-8 md:mb-12" />
				<h1 class="mb-4 font-mono text-3xl font-bold text-white md:text-4xl">
					Your Journey to Coding Conf 2025 Starts Here!
				</h1>
				<p class="text-gray-400">Secure your spot at next year's biggest coding conference.</p>
			</div>

			<form on:submit={handleSubmit} class="mx-auto space-y-6 md:max-w-md">
				<div class="space-y-2">
					<label for="avatar" class="block text-sm font-medium text-white">Upload Avatar</label>
					<div
						class="cursor-pointer rounded-lg border-2 border-dashed border-gray-600/50 bg-gray-900/20 p-6 text-center backdrop-blur-sm transition-colors duration-200 hover:border-gray-600/80"
						on:click={handleUploadClick}
						on:keydown={(e) => e.key === 'Enter' && handleUploadClick()}
						role="button"
						tabindex="0"
					>
						{#if avatarPreview}
							<img
								src={avatarPreview}
								alt="Avatar preview"
								class="mx-auto mb-2 h-24 w-24 rounded-lg"
							/>
							<div class="flex justify-center gap-2">
								<button
									type="button"
									class="rounded-md border border-gray-600/50 px-3 py-1 text-sm text-white backdrop-blur-sm transition-colors duration-200 hover:bg-gray-700/50"
									on:click|stopPropagation={() => {
										formData.avatar = null;
										avatarPreview = '';
									}}
								>
									Remove Image
								</button>
								<button
									type="button"
									class="rounded-md border border-gray-600/50 px-3 py-1 text-sm text-white backdrop-blur-sm transition-colors duration-200 hover:bg-gray-700/50"
								>
									Change Image
								</button>
							</div>
						{:else}
							<Upload
								class="mx-auto mb-2 h-10 w-10 rounded-lg border border-gray-600/50 bg-gray-900/30 p-2 text-center text-[#ff6b6b] backdrop-blur-lg"
							/>
							<p class="text-white">Drag and drop or click to upload</p>
							<p class="mt-1 text-sm text-gray-400">(JPG or PNG, max size: 500KB)</p>
						{/if}
					</div>
					<input
						type="file"
						id="avatar"
						accept="image/jpeg,image/png"
						class="hidden"
						on:change={handleFileChange}
						aria-describedby="avatar-error"
					/>
					{#if errors.avatar}
						<p class="text-sm text-[#ff6b6b]" id="avatar-error">{errors.avatar}</p>
					{/if}
				</div>

				<div class="space-y-2">
					<label for="fullName" class="block text-sm font-medium text-white">Full Name</label>
					<input
						type="text"
						id="fullName"
						bind:value={formData.fullName}
						class="w-full rounded-md border border-gray-600/60 bg-gray-900/20 px-4 py-2 text-white placeholder-gray-400 backdrop-blur-sm transition-all duration-200 focus:border-transparent focus:outline-none focus:ring-2 focus:ring-gray-600/50 md:h-12"
						aria-describedby="name-error"
					/>
					{#if errors.fullName}
						<p class="text-sm text-[#ff6b6b]" id="name-error">{errors.fullName}</p>
					{/if}
				</div>

				<div class="space-y-2">
					<label for="email" class="block text-sm font-medium text-white">Email Address</label>
					<input
						type="email"
						id="email"
						bind:value={formData.email}
						class="w-full rounded-md border border-gray-600/60 bg-gray-900/20 px-4 py-2 text-white placeholder-gray-400 backdrop-blur-sm transition-all duration-200 focus:border-transparent focus:outline-none focus:ring-2 focus:ring-gray-600/50 md:h-12"
						aria-describedby="email-error"
					/>
					{#if errors.email}
						<p class="text-sm text-[#ff6b6b]" id="email-error">{errors.email}</p>
					{/if}
				</div>

				<div class="space-y-2">
					<label for="github" class="block text-sm font-medium text-white">GitHub Username</label>
					<input
						type="text"
						id="github"
						bind:value={formData.githubUsername}
						class="w-full rounded-md border border-gray-600/60 bg-gray-900/20 px-4 py-2 text-white placeholder-gray-400 backdrop-blur-sm transition-all duration-200 focus:border-transparent focus:outline-none focus:ring-2 focus:ring-gray-600/50 md:h-12"
						aria-describedby="github-error"
					/>
					{#if errors.githubUsername}
						<p class="text-sm text-[#ff6b6b]" id="github-error">{errors.githubUsername}</p>
					{/if}
				</div>

				<button
					type="submit"
					class="group relative w-full overflow-hidden rounded-md bg-[#ff6b6b] px-4 py-2 text-white transition-colors duration-200 hover:bg-[#ff5252] md:h-11"
				>
					<span class="relative z-10">Generate My Ticket</span>
					<div
						class="absolute inset-0 bg-gradient-to-r from-[#ff6b6b] to-[#ff5252] opacity-0 transition-opacity duration-200 group-hover:opacity-100"
					></div>
				</button>
			</form>
		{:else}
			<div in:fade={{ duration: 200 }} class="text-center">
				<img src={fullLogo} alt="Coding Conf Logo" class="mx-auto mb-10 h-6 md:mb-12" />
				<h1 class="mb-4 text-3xl font-bold text-white md:text-4xl">
					<span class="text-white">Congrats, </span>
					<span class="text-[#ff6b6b] md:mb-2">{formData.fullName}!</span>
					<br class="hidden md:block" />
					<span class="text-white">Your ticket is ready.</span>
				</h1>
				<p class="mx-auto mb-8 text-gray-400 md:max-w-md">
					We've emailed your ticket to <span class="text-[#ff6b6b]">{formData.email}</span> and will
					send updates in the run up to the event.
				</p>

				<div
					class="group relative mx-auto mb-8 w-full bg-cover bg-center bg-no-repeat px-6 py-10 md:w-max"
					style="background-image: url({ptT}); background-size: 100% 100%"
					in:fly={{ y: 20, duration: 300, delay: 200 }}
				>

					<div class="flex items-center gap-4">
						<div class="flex flex-col items-start">
							<img src={fullLogo} alt="Coding Conf Logo" class="mb-2 h-6" />
							<div class="dl w-max font-mono text-sm text-gray-400">
								Jan 31, 2025 / Austin, TX
							</div>
						</div>
						<div
							class="tnt md:ml-[10%] mt-16 w-full rotate-90 text-center font-mono text-lg text-gray-400"
						>
							#{ticketNumber}
						</div>
					</div>
					<div class="mb-4 flex items-center gap-4">
						{#if avatarPreview}
							<img
								src={avatarPreview}
								alt=""
								class="h-12 w-12 rounded-lg ring-2 ring-gray-700/50"
							/>
						{/if}
						<div>
							<div class="font-bold text-white">{formData.fullName}</div>
							<div class="font-mono text-sm text-gray-400">@{formData.githubUsername}</div>
						</div>
					</div>
				</div>
			</div>
		{/if}
	</div>
</div>

<style>
	:global(body) {
		background: #1a1a2e;
		font-family: ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, 'Liberation Mono',
			'Courier New', monospace;
	}

	input:-webkit-autofill,
	input:-webkit-autofill:hover,
	input:-webkit-autofill:focus {
		-webkit-text-fill-color: white;
		-webkit-box-shadow: 0 0 0px 1000px #1a1a2e inset;
		transition: background-color 5000s ease-in-out 0s;
	}

	.bg-gradient::before {
		content: '';
		position: absolute;
		inset: 0;
		background: radial-gradient(circle at top right, #2d1b4e, transparent 50%),
			radial-gradient(circle at bottom left, #1a1a2e, transparent 50%);
		z-index: 0;
	}
  
  @media (max-width: 320px) {
    .dl{
      font-size: small;
    }
    .tnt {
      margin-left: -10px;
    }
  }

  @media (max-width: 596px) {
    .tnt {
      margin-left: 10%;
    }
  }
</style>
