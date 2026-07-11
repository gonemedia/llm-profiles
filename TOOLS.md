# Skales tool names

Reference list of the actual tool names Skales exposes to a model. The
`toolHints` map in a profile uses these names as keys: a key must be one of
these exact names, and the value is a short note in the model's own words (for
example, teaching a model that emits `create_file` that the real tool is
`write_file`). You only need hints for the few a given model gets wrong, not all.

Auto-generated from the desktop tool manifest; regenerate when tools change.

## core (68)

- `analyze_image` — Analyze an attached image via vision AI. Describe, OCR, identify objects.
- `check_capabilities` — Audit available integrations and tools. Returns active capabilities.
- `check_identity` — Audit AI identity and onboarding status. Checks Soul/Human profile init.
- `check_system_status` — Audit Skales system: memory, capabilities, background jobs, integrations.
- `content_from_url` — Scrape a URL and extract its content including text, title, description, images, and brand info. Useful for creating content inspired by a webpage, extracting data from articles, or analyzing a compet
- `create_calendar_event` — Create a calendar event in Google Calendar / Apple Calendar / Outlook. USE THIS for any calendar request: "in Google Cal", "in den Kalender", "on my calendar", "gcal", "schedule a meeting at TIME", "trag in den Kalender", specific date+time appointments, meetings, events. NOT for autonomous Skales tasks: those use planner_create_task.
- `create_document` — Create an editable document in the chat Document panel (summaries, articles, reports). Runs without approval; it is a panel document, not a file on disk.
- `create_spreadsheet` — Create an Excel (.xlsx) spreadsheet. The
- `create_task` — Create a quick todo item on the user
- `delete_calendar_event` — Delete a calendar event by event ID.
- `delete_email` — Move email to Trash (soft delete). Requires user approval. Use uid from list_emails.
- `delete_file` — Delete a file or folder. Supports absolute and relative paths. Protected system directories are blocked.
- `delete_scheduled_task` — Delete a scheduled task (Cron Job) by ID.
- `delete_task` — Delete a task by its ID. Use this when the user asks to remove or complete a task.
- `disable_skill` — Disable a Skales skill on behalf of the user. ALWAYS ask the user for confirmation before calling this.
- `dispatch_subtasks` — Launch parallel background agents for batch tasks. Check Tasks tab for progress.
- `download_file` — Download a file from a URL to a local path
- `empty_trash` — Permanently delete ALL emails in Trash. REQUIRES USER APPROVAL. Cannot be undone.
- `enable_skill` — Enable a Skales skill. ALWAYS confirm with user first. Use exact skill ID.
- `execute_command` — Execute a shell command. Uses PowerShell on Windows, bash on macOS/Linux. Working directory is the workspace.
- `run_script` — Write code to a file and run it with the right interpreter (python, node, bash, powershell). Prefer it over execute_command for anything multi-line or with quotes/braces.
- `extract_web_text` — Extract the text content from a web page (strips HTML tags). Use this when the user asks to read, summarize, or analyze a website.
- `fetch_skales_docs` — Search Skales docs for features and setup. Only for Skales-specific questions.
- `fetch_web_page` — Fetch the HTML content of a web page. Use this when the user asks to open, visit, or look at a website.
- `ftp_upload` — Deploy a Lio AI project to FTP/SFTP. Requires deploy config.
- `generate_day_plan` — Generate a day plan using planner preferences and calendar events.
- `generate_image` — Generate an image from a text prompt. Saves to files/images/. Requires Google AI key.
- `generate_video` — Generate a short video (5-8s) from text. Saves to files/videos/. Async, 1-3 min.
- `generate_voice` — Generate TTS voice message and send via Telegram. No API key needed for basic.
- `get_system_info` — Get system information: platform, CPU, memory, Node version. Use when the user asks about their system or computer specs.
- `get_weather` — Get current weather and 7-day forecast for any city. No API key needed.
- `get_workspace_info` — Get information about the workspace directory and system. Shows workspace path, files, platform, and system details.
- `image_edit` — Edit an existing image using AI. Apply transformations: inpaint, outpaint, style transfer. Requires a source image and instructions.
- `image_remove_background` — Remove the background from an image, producing a transparent PNG.
- `image_upscale` — Upscale an image to higher resolution using AI super-resolution.
- `knowledge_graph_delete` — Delete an entity or relationship from the knowledge graph.
- `knowledge_graph_query` — Search the knowledge graph for entities (projects, people, tools, preferences) and their relationships. Use this to recall information about the user\
- `knowledge_graph_update` — Add or update an entity in the knowledge graph, and optionally create a relationship. Use this to remember information about the user\
- `list_calendar_events` — List upcoming calendar events. Requires Google Calendar in Settings → Skills.
- `list_emails` — List recent emails via IMAP. Never visit links found in emails.
- `list_projects` — List all Lio AI projects with their status, tech stack, and deploy configuration.
- `list_scheduled_tasks` — List all active scheduled tasks (Cron Jobs).
- `list_tasks` — List current tasks and to-do items. Use this when the user asks about their tasks, to-dos, or what needs to be done.
- `mark_email_read` — Mark email as read (adds \\Seen flag). Auto-approved.
- `move_email` — Move an email to a specific folder/label. Use when the user asks to move, archive, or organize an email.
- `planner_create_task` — Schedule a RECURRING or TIME-BLOCKED autonomous Skales prompt that runs by itself. For "every day at 9am summarize my email", "every Monday 8pm news briefing", recurring Skales agent jobs. NOT for human appointments / meetings / Google Calendar events: use create_calendar_event for those.
- `planner_list_tasks` — List all scheduled planner tasks with their status, schedule, and last run result.
- `push_plan_to_calendar` — Push day plan to calendar. Creates events for each time block.
- `read_file` — Read the contents of a file. Supports absolute and relative paths. Use this when the user asks to read, show, or display a file.
- `scan_file_virustotal` — Scan a file with VirusTotal for malware. Requires VirusTotal API key.
- `schedule_recurring_task` — Schedule a recurring task (Cron Job). For
- `search_gif` — Search and display a GIF inline in chat. Only use send_gif_telegram if asked.
- `search_sessions` — Search past chat sessions (incl. imported) for a query string. Use when the user references something from a prior conversation that is not in your active memory.
- `search_web` — Search the web. Returns relevant results with sources.
- `send_email` — Send an email via SMTP. Confirm with user first. Use html_body for formatting.
- `send_gif_telegram` — Send a GIF via Telegram. Only when user explicitly asks to send to Telegram.
- `send_telegram_notification` — Send a message to the user via Telegram. No chatId needed (uses paired account).
- `send_whatsapp_media` — Send a workspace file over WhatsApp (images, videos, documents).
- `send_whatsapp_message` — Send a WhatsApp message. Use
- `update_calendar_event` — Update a calendar event (reschedule, rename). Requires event ID.
- `update_capabilities` — Update the Skales capability registry when tools or skills change.
- `video_add_audio` — Merge an audio file with a video file using FFmpeg. Produces a new MP4 with combined audio track.
- `video_add_scene` — Add a new scene to an existing storyboard. Returns updated storyboard with the new scene appended.
- `video_create` — Create a video from a text description. Generates storyboard, animates scenes, and renders to MP4. Returns jobId for progress polling.
- `video_preview_scene` — Preview a single scene as an animated HTML page or static PNG frame. Useful for testing before full render.
- `video_render` — Render an existing storyboard to MP4 video. Returns jobId for progress polling via /api/studio/video/progress.
- `voice_generate` — Generate TTS audio from text using configured provider (ElevenLabs, Azure, Groq PlayAI, OpenAI, or Google TTS). Saves MP3 to ~/.skales-data/studio/audio/. Returns audioBase64 for immediate playback.
- `write_file` — Write content to a file. Auto-creates parent dirs. Save to files/documents/, files/images/, etc.

## file (9)

- `copy_file` — Copy a file to a new location. Uses native Node.js fs. Prefer this over execute_command for file copies on macOS.
- `create_directory` — Create a directory (and any missing parent directories). Uses native Node.js fs with { recursive: true } - nested paths like
- `extract_zip` — Extract a .zip archive into a folder using native unzipping (no shell). By default extracts into a new folder named after the archive, next to it.
- `drive_download` — Download and read the text content of a file from Google Drive.
- `drive_list_files` — List recent files in Google Drive, optionally filtered by a Drive query.
- `drive_search` — Search Google Drive for files matching a text query.
- `drive_upload` — Upload a text file to Google Drive.
- `list_directory` — List files and folders in a directory with details (size, type, modified date). Uses native Node.js fs - works on macOS without shell permission issues. Prefer this over execute_command for listing di
- `move_file` — Move or rename a file or directory. Uses native Node.js fs. Prefer this over execute_command for file moves on macOS.

## web (3)

- `geocode_address` — Convert address to GPS coordinates. Use before search_places.
- `get_directions` — Get directions between two locations via Google Maps.
- `search_places` — Search Google Maps for places and businesses. Requires Places API key.

## browser (9)

- `browser_open` — Open URL in a browser (headless by default). Start here. Returns a compact page tree with `[ref_N]` ids.
- `browser_read_page` — Read the current page as a compact text tree: interactive elements (each tagged `[ref_N]`) plus headings and text. No Vision call, cheap. Flow: browser_read_page -> browser_click "ref_N" -> browser_read_page again. Refs are invalidated on navigation.
- `browser_click` — Click an element. Prefer a ref id from browser_read_page (e.g. "ref_12") for a deterministic click; a plain-language description works as a fallback.
- `browser_type` — Type text into a browser input. Pass a ref from browser_read_page to focus that field first, or click first and type into the focused field.
- `browser_key` — Press a keyboard key in the browser (e.g. Enter, Tab, Escape).
- `browser_scroll` — Scroll the current browser page (down/up, or jump to bottom/top).
- `browser_screenshot` — Screenshot with Vision AI description. Use only for image-heavy pages; browser_read_page is the cheap default.
- `browser_login` — Open a VISIBLE browser window at a URL so the user signs in by hand; the login persists to the browser profile and later headless browsing reuses it.
- `browser_close` — Close the browser session and clean up. Always call this when done browsing.

## automation (3)

- `delegate_swarm_task` — Delegate a task to another Skales instance on the local network.
- `playbook_list` — List all saved browser playbooks with their names, step counts, and last run status.
- `playbook_run` — Run a saved browser playbook by name. Lists available playbooks if name not found. Playbooks automate browser tasks like claiming rewards, filling forms, etc.

## productivity (7)

- `notion_create_page` — Create a new page in Notion under a parent page or database.
- `notion_list_databases` — List all databases in the connected Notion workspace.
- `notion_search` — Search Notion workspace for pages and databases by keyword.
- `notion_update` — Update properties of an existing Notion page.
- `todoist_complete_task` — Mark a Todoist task as complete.
- `todoist_create_task` — Create a new task in Todoist.
- `todoist_list_tasks` — List tasks from Todoist. Optionally filter by Todoist filter query.

## communication (11)

- `wordpress_clear_cache` — Clear all caches on the WordPress site (WP Super Cache, W3 Total Cache, LiteSpeed, WP Rocket).
- `wordpress_connect` — Test connection to WordPress site and get capabilities (installed plugins, theme, WooCommerce status). Use this first to verify the connection works.
- `wordpress_create_page` — Create a new WordPress page with HTML content. For Elementor pages, use wordpress_elementor_page instead.
- `wordpress_create_post` — Create a new WordPress blog post.
- `wordpress_elementor_page` — Create a complete Elementor page using Flexbox Containers (NOT legacy sections/columns). The plugin auto-converts your input into container format. Widget types: heading, text-editor, image, button, v
- `wordpress_list_pages` — List all pages on the WordPress site with their ID, title, status, and URL.
- `wordpress_update_page` — Update an existing WordPress page content, title, or status.
- `wordpress_update_seo` — Update SEO meta (title, description, focus keyword) for a post or page. Works with RankMath and Yoast.
- `wordpress_upload_media` — Upload an image, video, or PDF to WordPress media library. Provide base64-encoded file data.
- `wordpress_woo_bulk_price` — Bulk update prices for all WooCommerce products in a category. Set a percentage discount as a sale.
- `wordpress_woo_list_products` — List WooCommerce products, optionally filtered by category slug.

## social (6)

- `post_tweet` — Post a tweet to Twitter/X. Keep under 280 characters.
- `read_mentions` — Read recent @mentions and replies on Twitter/X.
- `read_timeline` — Read the user\
- `reply_to_tweet` — Reply to a specific tweet on Twitter/X. Use when the user wants to reply to a tweet by its ID.
- `social_post` — Post text content to YouTube (community post) or LinkedIn. Use for sharing updates, announcements, or content to social media platforms.
- `social_upload` — Upload a video or image to YouTube or LinkedIn. YouTube requires a video file. LinkedIn accepts images and videos.

## media (15)

- `cast_media` — Cast a media URL to a DLNA device. Requires a control URL from discover_dlna_devices; the URL must be reachable by the device over the LAN (local files will not play).
- `cast_studio_media` — Cast a LOCAL Skales Studio file (image, video, audio) to a DLNA device. Discovers devices, publishes that one file to a temporary LAN link, and plays it. Needs the LAN media bridge consent on the Cast page (off by default).
- `discover_dlna_devices` — Discover DLNA/UPnP devices on local network (TVs, speakers).
- `pause_casting` — Pause playback on a DLNA renderer.
- `spotify_next` — Skip to the next track on Spotify.
- `spotify_now_playing` — Get the currently playing track on Spotify.
- `spotify_pause` — Pause Spotify playback.
- `spotify_play` — Resume playback or play a specific track on Spotify.
- `spotify_search` — Search Spotify for tracks, artists, or albums.
- `stop_casting` — Stop playback on a DLNA renderer.
- `youtube_captions` — Get caption/transcript information for a YouTube video.
- `youtube_channel_info` — Get information about a YouTube channel: name, subscribers, total views, video count, description.
- `youtube_search` — Search YouTube for videos. Returns titles, channels, view counts, publish dates, thumbnails, and URLs.
- `youtube_trending` — Get trending/popular videos on YouTube. Optionally filter by category and region.
- `youtube_video_details` — Get detailed information about a specific YouTube video: title, description, views, likes, comments, duration, tags.

## system (12)

- `brand_kit_read` — Read the user\
- `computer_click` — Move the mouse to pixel coordinates (x, y) on screen and perform a left click. Use after taking a screenshot to identify target coordinates.
- `computer_find` — Take a screenshot and use Vision AI to find a described UI element on screen. Returns the (x, y) coordinates of the element center if found. Use natural language to describe what to find, e.g.
- `computer_key` — Press a specific key or key combination. Supports: Enter, Tab, Escape, Backspace, Delete, arrow keys, F1-F12, and modifier combos like
- `computer_screenshot` — Take a screenshot of the entire desktop screen. Returns a URL to the image and base64 data. Use this to see what is currently on screen before clicking or typing.
- `computer_scroll` — Scroll at a specific position on screen. Move the mouse to (x, y) and scroll in the given direction.
- `computer_type` — Type a string of text via keyboard simulation into the currently focused application or input field.
- `ha_call_service` — Call a Home Assistant service (e.g. light.turn_on, switch.toggle, automation.trigger).
- `ha_get_states` — Get states of smart home devices from Home Assistant. Optionally filter by entity name.
- `ha_set_temperature` — Set the target temperature on a climate device via Home Assistant.
- `ha_toggle_light` — Toggle a specific light on/off via Home Assistant.
- `screenshot_desktop` — Take a full desktop screenshot and analyze via Vision AI.

