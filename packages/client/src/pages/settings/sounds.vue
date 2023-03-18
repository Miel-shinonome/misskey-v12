<template>
<div class="_formRoot">
	<FormRange v-model="masterVolume" :min="0" :max="1" :step="0.05" :text-converter="(v) => `${Math.floor(v * 100)}%`" class="_formBlock">
		<template #label>{{ i18n.ts.masterVolume }}</template>
	</FormRange>

	<FormSection>
		<template #label>{{ i18n.ts.sounds }}</template>
		<FormLink v-for="type in Object.keys(sounds)" :key="type" style="margin-bottom: 8px;" @click="edit(type)">
			{{ $t('_sfx.' + type) }}
			<template #suffix>{{ sounds[type].type || i18n.ts.none }}</template>
			<template #suffixIcon><i class="fas fa-chevron-down"></i></template>
		</FormLink>
	</FormSection>

	<FormButton danger class="_formBlock" @click="reset()"><i class="fas fa-redo"></i> {{ i18n.ts.default }}</FormButton>
</div>
</template>

<script lang="ts" setup>
import { computed, ref } from 'vue';
import FormRange from '@/components/form/range.vue';
import FormButton from '@/components/MkButton.vue';
import FormLink from '@/components/form/link.vue';
import FormSection from '@/components/form/section.vue';
import * as os from '@/os';
import { ColdDeviceStorage } from '@/store';
import { playFile } from '@/scripts/sound';
import { i18n } from '@/i18n';
import { definePageMetadata } from '@/scripts/page-metadata';

const masterVolume = computed({
	get: () => {
		return ColdDeviceStorage.get('sound_masterVolume');
	},
	set: (value) => {
		ColdDeviceStorage.set('sound_masterVolume', value);
	},
});

const volumeIcon = computed(() => masterVolume.value === 0 ? 'fas fa-volume-mute' : 'fas fa-volume-up');

const sounds = ref({
	note: ColdDeviceStorage.get('sound_note'),
	noteMy: ColdDeviceStorage.get('sound_noteMy'),
	notification: ColdDeviceStorage.get('sound_notification'),
	chat: ColdDeviceStorage.get('sound_chat'),
	chatBg: ColdDeviceStorage.get('sound_chatBg'),
	antenna: ColdDeviceStorage.get('sound_antenna'),
	channel: ColdDeviceStorage.get('sound_channel'),
});

const soundsTypes = [
	null,
	'syuilo/up',
	'syuilo/down',
	'syuilo/pope1',
	'syuilo/pope2',
	'syuilo/waon',
	'syuilo/popo',
	'syuilo/triple',
	'syuilo/poi1',
	'syuilo/poi2',
	'syuilo/pirori',
	'syuilo/pirori-wet',
	'syuilo/pirori-square-wet',
	'syuilo/square-pico',
	'syuilo/reverved',
	'syuilo/ryukyu',
	'syuilo/kick',
	'syuilo/snare',
	'syuilo/queue-jammed',
	'aisha/1',
	'aisha/2',
	'aisha/3',
	'noizenecio/kick_gaba',
	'noizenecio/kick_gaba2',
	'ffxiv/se_1',
	'ffxiv/se_2',
	'ffxiv/se_3',
	'ffxiv/se_4',
	'ffxiv/se_5',
	'ffxiv/se_6',
	'ffxiv/se_7',
	'ffxiv/se_8',
	'ffxiv/se_9',
	'ffxiv/se_10',
	'ffxiv/se_11',
	'ffxiv/se_12',
	'ffxiv/se_13',
	'ffxiv/se_14',
	'ffxiv/se_15',
	'ffxiv/se_16',
	'samsung/Alpa',
	'samsung/Conclusion',
	'samsung/Crystal',
	'samsung/Glitter',
	'samsung/Guitar',
	'samsung/Harmonica',
	'samsung/Lucid',
	'samsung/Milky_Way',
	'samsung/Red_Dwarf',
	'samsung/Spaceline',
	'samsung/Sticky',
	'samsung/Voyager',
	'samsung/coin',
	'samsung/hop',
	'resette/nobiru_mail',
	'resette/mail_fuka',
	'resette/mail_miel',
	'resette/mail_nonoka',
	'resette/mail_yuzuki',
	'resette/message_fuka',
	'resette/message_miel',
	'resette/message_nonoka',
	'resette/message_yuzuki',
	'resette/reply_fuka',
	'resette/reply_miel',
	'resette/reply_nonoka',
	'resette/reply_yuzuki',
];

async function edit(type) {
	const { canceled, result } = await os.form(i18n.t('_sfx.' + type), {
		type: {
			type: 'enum',
			enum: soundsTypes.map(x => ({
				value: x,
				label: x == null ? i18n.ts.none : x,
			})),
			label: i18n.ts.sound,
			default: sounds.value[type].type,
		},
		volume: {
			type: 'range',
			min: 0,
			max: 1,
			step: 0.05,
			textConverter: (v) => `${Math.floor(v * 100)}%`,
			label: i18n.ts.volume,
			default: sounds.value[type].volume,
		},
		listen: {
			type: 'button',
			content: i18n.ts.listen,
			action: (_, values) => {
				playFile(values.type, values.volume);
			},
		},
	});
	if (canceled) return;

	const v = {
		type: result.type,
		volume: result.volume,
	};

	ColdDeviceStorage.set('sound_' + type, v);
	sounds.value[type] = v;
}

function reset() {
	for (const sound of Object.keys(sounds.value)) {
		const v = ColdDeviceStorage.default['sound_' + sound];
		ColdDeviceStorage.set('sound_' + sound, v);
		sounds.value[sound] = v;
	}
}

const headerActions = $computed(() => []);

const headerTabs = $computed(() => []);

definePageMetadata({
	title: i18n.ts.sounds,
	icon: 'fas fa-music',
});
</script>
