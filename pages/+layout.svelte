<script>
	import '@evidence-dev/tailwind/fonts.css';
	import '../app.css';
	import { EvidenceDefaultLayout } from '@evidence-dev/core-components';
	export let data;
</script>

<EvidenceDefaultLayout {data}  > <!-- darkLogo="/Entrador-logo-wit.svg" lightLogo="/blauw.png"> -->
	<slot slot="content" />
</EvidenceDefaultLayout>
