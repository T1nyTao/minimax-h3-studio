---
name: minimax-h3-studio
description: Unified MiniMax H3 prompt workflow for rough ideas, scripts, text-to-video, keyframes, multi-reference assets, dialogue, sound, and multi-segment generation. Use when the user wants an H3 prompt normalized, expanded, repaired, segmented, or written in a native three-field or six-section format; it writes prompts and does not generate video files.
---

# MiniMax H3 Studio

Turn any H3 request into a directly usable prompt while preserving the user's intent, source language, asset roles, timing, continuity, and requested output format. This skill combines beginner-friendly prompt normalization, production planning, native English T2VA/I2VA/FL2VA/L2VA prompts, and full-reference six-section prompts.

## 1. Intake and asset inspection

Accept a one-line idea, rough prompt, story, screenplay, dialogue, advertisement concept, shot list, or supplied image/video/audio assets. Infer reasonable defaults and state only material assumptions. Ask at most one concise question when a missing answer would change the mode, asset role, duration, or deliverable; otherwise proceed.

When assets are present, inspect the actual files with available tools before describing them. Track upload order and assign stable labels. Do not claim to see, hear, or measure an asset that was not supplied or inspected. Ignore unwanted source backgrounds, poses, watermarks, interface text, and compression artifacts when the user asks to replace them.

For every asset, determine its role rather than inferring from file type alone:

- identity, costume, environment, prop, style, action, camera, storyboard, or whole-video structure;
- concrete first frame, keyframe, last frame, or composition anchor;
- source video to edit or continue;
- audio to copy, partially reuse, or use only as a timbre/style/rhythm reference.

A character turnaround is one character with multiple views. An image used only for identity is not automatically a `<Picture N>` keyframe. A video containing sound does not automatically create an audio reference.

## 2. Choose the production mode

Use the most specific mode requested by the user, then the role of the supplied assets:

1. **Standardizer mode** for a beginner-facing Chinese prompt, a messy input, expansion, shortening, repair, shot planning, or a story that needs segmentation. Use this when no native H3 template is requested.
2. **Text/keyframe mode** for a native English three-field prompt:
   - **T2VA** when text defines the video and there is no concrete frame anchor;
   - **I2VA** when one supplied image is the actual opening frame;
   - **FL2VA** when supplied images are the actual first and last frames;
   - **L2VA** when one supplied image is the actual final frame.
3. **Full-reference mode** for character or scene references, multiple image/video/audio roles, subject replacement, source-video editing or continuation, reference-audio reuse, or any mixed asset relationship that should be tracked explicitly. Use the six-section English template.

If a request combines identity references with a concrete keyframe, treat the identity sources as subject provenance and the keyframe as a picture anchor; use full-reference mode when the relationships need explicit tracking. Never force a turnaround or identity image into I2VA/FL2VA/L2VA merely because it is an image.

If the user explicitly requests Chinese, English, bilingual, a named template, or only the final prompt, honor that request. Native template constraints take precedence over the standardizer wrapper. For bilingual output, provide aligned Chinese and English versions and keep dialogue and visible text in their original language.

## 3. Standardizer mode

Before writing, silently decide generation mode, story mode, shot structure, duration, and whether segmentation is necessary. Use the user's duration; otherwise choose a coherent duration up to 15 seconds per generation and disclose the assumption briefly.

Keep each segment a complete micro-event or emotional beat. Split only at a visible change of shot size, viewpoint, location, time, subject, or completed action. Do not split an unfinished pose, action phase, composition, or emotional state across independent generations. Prefer a longer coherent segment when the action load remains controllable.

Write observable instructions in this order: subject and core event; scene and continuity state; timeline or shot flow with concrete actions and endpoints; camera behavior; lighting, color, texture, and style; dialogue, lip sync, ambience, action sounds, and music; then only relevant stability constraints. Replace vague adjectives with visible composition, motion, color, lighting, rhythm, and texture. For action, describe direction, body weight, cause, reaction, environment interaction, and finishing pose. For dialogue, describe gaze, breathing, pauses, micro-expression, vocal tone, exact words, and lip sync.

Default standardizer output:

```text
【任务设定】
时长｜比例｜模式｜核心事件

【图片上传顺序】
仅在有参考图时输出

【MiniMax H3 主提示词】
完整、可复制的中文生产提示词

【防错约束】
简短列出最相关的限制
```

For a story or request longer than one generation, use an overall continuity block followed by complete segments, each with upload references, a full prompt, and an explicit end-frame state and next cut point. Do not mechanically add sections that do not help. If the user asks for only the final prompt, omit explanations and wrappers.

## 4. Native text/keyframe mode

Read [references/video-prompt-guide.md](references/video-prompt-guide.md) before writing. For the exact Chinese planning counterpart, consult [references/standardizer-text-keyframe-zh.md](references/standardizer-text-keyframe-zh.md) only when needed.

Write the production prompt in English and preserve dialogue, lyrics, and visible text in the original language. Use this exact field order:

```text
integrated_multimodal_description: [Shot 1] ...

overall_soundscape: ...

non_diegetic_music: ...
```

T2VA starts directly with those fields. I2VA, FL2VA, and L2VA prepend the exact alignment instruction from the guide, then one blank line and the same three fields. Use the actual final shot number and effective duration with two decimal places in L2VA and FL2VA alignment lines.

Start `[Shot 1]` without a timestamp. Later cuts use sequential `[Shot N] At MM:SS.mmm, ...` with strictly increasing times within the duration. Keep camera motion natural within each shot; a phase of one continuous shot is not a new cut. For keyframes, describe the observable path from or toward the frame anchor instead of repeating two static image descriptions.

Assign stable `(S1)`, `(S2)`, ... IDs only to vocal sources in target-event order and reuse them across shots. Put only the language tag and exact spoken words inside `<d>`. Follow the guide for voiceover with closed lips, `<scenetrans>`, `<cutoff>`, compound speaker IDs, on-screen text, and diegetic sound. `overall_soundscape` covers ambience, physical action sounds, and non-verbal human sounds; `non_diegetic_music` covers audience-only score. Use `N/A` only for explicit complete silence or no audience-only score.

## 5. Full-reference mode

Read [references/full-reference-guide.md](references/full-reference-guide.md) and its linked shared guide before writing. Consult [references/standardizer-multi-reference-zh.md](references/standardizer-multi-reference-zh.md) when the user wants the beginner-facing Chinese multi-reference planning form.

Write all six sections in English, in this exact order:

```text
subject_definitions:
...

summary:
[task type] ...

retention_analysis:
...

detailed_description:
One or two sentences establish the visual style.
[Shot 1] ...

overall_soundscape:
...

non_diegetic_music:
...
```

Use `<Subject N>` for reusable visible content, standalone `<Picture N>` only for actual frame/keyframe/composition anchors, `<Video N>` for whole-video editing/continuation/temporal structure, and `<Audio N>` only for audio that is copied or explicitly referenced. Video and audio numbering are independent. Define only separately tracked content and keep labels stable across every section.

Choose task-type prefixes from `keyframe completion`, `reference generation`, `video editing`, `video continuation`, `audio reuse`, and `audio reference`, combining applicable types with ` + ` without duplicates. For direct video edits, begin the summary after the prefix with `The target video is an edited version of <Video 1>.` Use one retention line per defined item. Visible references use only `fully_preserved`, `partially_preserved`, `attribute_transfer`, or `weak_reference`; audio uses only `fully_copy`, `partially_copy`, `reference`, or `weak_reference`, judged within the role defined for that item.

Make `detailed_description` a playable audiovisual timeline rather than a plot synopsis or reference list. Establish composition, visible features and positions, environment, light, actions, reactions, camera movement, sound, and where each reference takes effect. Normally use 350–500 English words for generation tasks, while dialogue-heavy tasks prioritize complete spoken timing and video edits scale to their actual complexity. Put style introduction before `[Shot 1]`. Use the shared guide for shot syntax, dialogue tags, visible text, cross-cut speech, and closed-lip voiceover.

Assign vocal IDs once in target-event order. A speaking referenced subject is written as `<Subject N> (Sx)`; do not put `(Sx)` in retention lines. Vocal content that exists only inside directly copied soundtrack may use `<Audio N>` without inventing a speaker. Preserve exact source words and language for reused or explicitly reperformed dialogue and lyrics; use `[unclear]` rather than guessing.

Keep dialogue and lyrics only in `detailed_description`; do not repeat them in `overall_soundscape` or `non_diegetic_music`. Put ambience and physical sounds in the former and audience-only score in the latter. Do not call audio `fully_copy` while adding or replacing layers.

## 6. Cross-mode quality and reliability pass

Before delivering, verify:

- selected mode matches the user's requested output and actual asset roles;
- requested duration, aspect ratio, language, dialogue, lyrics, visible text, and character-to-reference assignments are preserved;
- shot numbers and timestamps are valid and the action path has clear endpoints;
- camera direction, axis, identity, clothing, props, lighting, and frame continuity remain stable;
- speaker IDs are consistent and audio is separated into diegetic, ambience/physical, and non-diegetic layers;
- standardizer output is directly copyable, while native prompts use their exact field/section order;
- constraints target real failure risks such as identity drift, face or clothing changes, prop duplication, malformed hands, lip-sync errors, teleporting action, axis reversal, camera jumps, lighting drift, unwanted text, or style drift.

Never invent observations, translations, source words, or guaranteed outcomes. When the input is overloaded, simplify simultaneous actions, reduce camera complexity, or split at a genuine visual boundary. Keep any upload mapping or material assumption outside the copyable native prompt unless the selected template requires it.

