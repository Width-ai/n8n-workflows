# Our n8n Template Workflows

Full repo of real n8n workflows we use on a daily basis for internal use or customers. 

## n8n marketing automation examples
automated content creation via wordpress rss: https://github.com/Width-ai/n8n-workflows/blob/main/automated-content-creation-via-wordpress-rss

keyword rank tracker: https://github.com/Width-ai/n8n-workflows/blob/main/keyword_rank_tracker_google_sheets.json

google search console ai seo optimization: https://github.com/Width-ai/n8n-workflows/blob/main/gsc_ai_seo_writer.json

Product data enrichment for PDP creation via Pumice, with product matching to verify source pages: https://github.com/Width-ai/n8n-workflows/blob/main/pumice-product-data-enrichment-workflow-n8n.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/pumice-product-data-enrichment-workflow-n8n.md)

AI product title optimization via Pumice, with title rules, examples, length validation, and product matching to verify source pages: https://github.com/Width-ai/n8n-workflows/blob/main/pumice-ai-product-title-optimization-workflow-n8n.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/pumice-ai-product-title-optimization-workflow-n8n.md)

AI product description generation via Pumice, with description rules, examples, word count validation, and product matching to verify source pages: https://github.com/Width-ai/n8n-workflows/blob/main/pumice-ai-product-description-generation-workflow-n8n.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/pumice-ai-product-description-generation-workflow-n8n.md)

Amazon price tracker API via Pumice, with a live price lookup endpoint, a daily Google Sheets watchlist, price history, and Gmail price drop alerts: https://github.com/Width-ai/n8n-workflows/blob/main/pumice-amazon-price-tracker-api-workflow-n8n.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/pumice-amazon-price-tracker-api-workflow-n8n.md)

Amazon competitor price tracking API via Pumice, with a Google Sheets list of competitor ASINs, products, or competitor names priced daily, product matching to confirm products found by name, latest prices and price history, an API that returns stored competitor prices, and a daily Gmail summary of price and stock changes: https://github.com/Width-ai/n8n-workflows/blob/main/pumice-amazon-competitor-price-tracking-api-workflow-n8n.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/pumice-amazon-competitor-price-tracking-api-workflow-n8n.md)

Track competitor prices API via Pumice for any website, with site: searches by SKU, UPC, or MPN, product matching to confirm each competitor page, daily prices and price history in Google Sheets, an API that compares stored prices across competitors, and a daily Gmail summary of price and stock changes: https://github.com/Width-ai/n8n-workflows/blob/main/pumice-track-competitor-prices-api-workflow-n8n.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/pumice-track-competitor-prices-api-workflow-n8n.md)

AI content humanizer for blog posts, using the content-humanizer skill with Claude to remove AI writing patterns in draft, audit, and final passes, with checks that keep headings, links, keywords, and code intact, and an optional Google Doc: https://github.com/Width-ai/n8n-workflows/blob/main/ai-content-humanizer-blog-workflow-n8n.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/ai-content-humanizer-blog-workflow-n8n.md)

Blog post to social media posts: https://github.com/Width-ai/n8n-workflows/blob/main/blog-to-social-ai-free.json

Multi-platform social media content creation with context documents, blog summaries, and Instagram performance rules: https://github.com/Width-ai/n8n-workflows/blob/main/n8n-social-media-content-creation.json

Instagram and Facebook video downloader Telegram bot with a Google Drive and Google Sheets content library: https://github.com/Width-ai/n8n-workflows/blob/main/Download%20Instagram%20%26%20Facebook%20Videos_Reels%20with%20Telegram%20Bot.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/Download%20Instagram%20%26%20Facebook%20Videos_Reels%20with%20Telegram%20Bot.md)

Download TikTok videos without watermarks with an n8n form, plus bulk downloads from Google Sheets to Google Drive: https://github.com/Width-ai/n8n-workflows/blob/main/Download%20TikTok%20Videos%20Without%20Watermarks.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/Download%20TikTok%20Videos%20Without%20Watermarks.md)

Facebook spy tool that scrapes competitor ads from the Meta Ad Library with Apify, analyzes each creative with OpenAI, and logs the breakdowns to Google Sheets: https://github.com/Width-ai/n8n-workflows/blob/main/ai-facebook-ad-spy-tool.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/ai-facebook-ad-spy-tool.md)

Automate Google reviews with AI replies to Google Business Profile reviews, Slack approval for low ratings, and Google Sheets logging: https://github.com/Width-ai/n8n-workflows/blob/main/automate-google-reviews-ai-replies.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/automate-google-reviews-ai-replies.md)

## n8n event automation examples
QR ticket scanner and validator with instant check-in results, duplicate detection, ticket rules, and alerts: https://github.com/Width-ai/n8n-workflows/blob/main/Validate%20QR%20tickets%20in%20real%20time%20with%20n8n%20Forms%20and%20Google%20Sheets.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/Validate%20QR%20tickets%20in%20real%20time%20with%20n8n%20Forms%20and%20Google%20Sheets.md)

## n8n AI chatbot examples
AI WhatsApp chatbot that answers text, voice notes, images, and PDFs with OpenAI, with spoken replies to voice notes and per-user memory: https://github.com/Width-ai/n8n-workflows/blob/main/AI-Powered%20WhatsApp%20Chatbot%20for%20Text%2C%20Voice%2C%20Images%20%26%20PDFs.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/AI-Powered%20WhatsApp%20Chatbot%20for%20Text%2C%20Voice%2C%20Images%20%26%20PDFs.md)

AI lead generation agent that finds local businesses with Google Places, researches their websites with Pumice, scores fit, and saves qualified leads to Google Sheets: https://github.com/Width-ai/n8n-workflows/blob/main/ai-lead-generation-agent.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/ai-lead-generation-agent.md)

## n8n CRM and ERP integration examples
HubSpot to NetSuite integration that turns Closed Won deals into NetSuite sales orders, matches or creates customers by email, syncs invoice payment status back to HubSpot deals, and posts errors to Slack: https://github.com/Width-ai/n8n-workflows/blob/main/hubspot-to-netsuite-sales-order-and-invoice-sync.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/hubspot-to-netsuite-sales-order-and-invoice-sync.md)

Connect Facebook Lead Ads and Google Ads lead forms to Google Sheets and Salesforce, with duplicate lead checks and sync status on each row: https://github.com/Width-ai/n8n-workflows/blob/main/connect-facebook-lead-ads-google-sheets-salesforce.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/connect-facebook-lead-ads-google-sheets-salesforce.md)

Automate sales proposals from call transcripts, with AI-written scope, catalog pricing, a Google Doc proposal, and a Gmail draft for the client: https://github.com/Width-ai/n8n-workflows/blob/main/automate-sales-proposals-from-call-transcripts.json (guide: https://github.com/Width-ai/n8n-workflows/blob/main/guides/automate-sales-proposals-from-call-transcripts.md)
