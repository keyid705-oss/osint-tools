import requests
import json
import tempfile
import os
from urllib.parse import urlparse, parse_qs, unquote
from datetime import datetime, timezone


# config
WEBHOOK_URL = "https://discord.com/api/webhooks/1542235359700852890/DaqfDcARSArAQKFpEy7QJO4rGheCDNbNfnW8znsxrrRRYeYrUqW7fNrqUfLAratQFSBg"


parameter_names = {
    "sharer_language": "Language",
    "share_app_id": "App ID",
    "source": "Share source",
    "utm_source": "UTM source",
    "utm_campaign": "Campaign",
    "utm_medium": "Medium",
    "item_author_type": "Author type",
    "share_enter_from": "Entry point",
    "ugbiz_name": "Business context",
    "ug_btm": "Business tracking",
    "user_id": "TikTok user ID",
    "sec_user_id": "Secure user ID",
    "u_code": "User code",
    "share_iid": "Share instance ID",
    "share_item_id": "Share item ID",
    "share_link_id": "Share link ID",
    "timestamp": "Timestamp",
    "preview_pb": "Preview PB",
    "panel_source_v2": "Panel source",
    "sp_level": "SP level",
    "social_share_type": "Social share type",
    "enable_checksum": "Checksum enabled",
    "sp_root_share_link_id": "Root share link ID",
    "sp_root_u": "Root user code",
    "sp_root_d": "Root share code",
    "link_reflow_popup_iteration_sharer": "Share popup settings"
}


def expand_tiktok_url(url):
    try:
        response = requests.get(
            url,
            allow_redirects=True,
            headers={
                "User-Agent": (
                    "Mozilla/5.0 (Windows NT 10.0; Win64; x64) "
                    "AppleWebKit/537.36 "
                    "(KHTML, like Gecko) "
                    "Chrome/151.0.0.0 Safari/537.36"
                )
            },
            timeout=15
        )

        return response.url

    except requests.RequestException as error:
        print(f"Could not expand TikTok URL: {error}")
        return url


def extract_metadata(url):
    parsed = urlparse(url)
    params = parse_qs(parsed.query)

    fields = []

    username = None
    video_id = None

    # get the creator username
    if "/@" in parsed.path:
        username = parsed.path.split("/@")[1].split("/")[0]

        if username:
            username = "@" + username

    # get the video ID
    if "/video/" in parsed.path:
        video_id = parsed.path.split("/video/")[1].split("/")[0]

    if username:
        fields.append(
            ("Video creator", username)
        )

    if video_id:
        fields.append(
            ("Video ID", video_id)
        )

    # get parameters from the URL
    for parameter, label in parameter_names.items():
        value = params.get(parameter)

        if not value:
            continue

        value = value[0]

        if parameter == "link_reflow_popup_iteration_sharer":
            value = unquote(value)

        fields.append(
            (label, value)
        )

    # convert Unix timestamp to UTC
    timestamp = params.get("timestamp")

    if timestamp:
        try:
            date = datetime.fromtimestamp(
                int(timestamp[0]),
                timezone.utc
            )

            fields.append(
                (
                    "Timestamp date UTC",
                    date.strftime("%Y-%m-%d %H:%M:%S")
                )
            )

        except (ValueError, OverflowError):
            pass

    return fields


def get_value(fields, name):
    for field_name, value in fields:
        if field_name == name:
            return value

    return "Unknown"


def normal_output(fields):
    return "\n".join(
        f"{name}: {value}"
        for name, value in fields
    )


def specialized_output(fields):
    values = dict(fields)

    creator = values.get("Video creator", "Unknown")
    video_id = values.get("Video ID", "Unknown")
    language = values.get("Language", "Unknown")
    app_id = values.get("App ID", "Unknown")
    share_source = values.get("Share source", "Unknown")
    utm_source = values.get("UTM source", "Unknown")
    campaign = values.get("Campaign", "Unknown")
    medium = values.get("Medium", "Unknown")
    author_type = values.get("Author type", "Unknown")
    entry_point = values.get("Entry point", "Unknown")
    business_context = values.get("Business context", "Unknown")
    business_tracking = values.get(
        "Business tracking",
        "Unknown"
    )

    user_id = values.get(
        "TikTok user ID",
        "Unknown"
    )

    secure_id = values.get(
        "Secure user ID",
        "Unknown"
    )

    user_code = values.get(
        "User code",
        "Unknown"
    )

    share_instance = values.get(
        "Share instance ID",
        "Unknown"
    )

    share_item = values.get(
        "Share item ID",
        "Unknown"
    )

    share_link = values.get(
        "Share link ID",
        "Unknown"
    )

    timestamp = values.get(
        "Timestamp",
        "Unknown"
    )

    timestamp_date = values.get(
        "Timestamp date UTC",
        "Unknown"
    )

    preview_pb = values.get(
        "Preview PB",
        "Unknown"
    )

    panel_source = values.get(
        "Panel source",
        "Unknown"
    )

    sp_level = values.get(
        "SP level",
        "Unknown"
    )

    social_type = values.get(
        "Social share type",
        "Unknown"
    )

    checksum = values.get(
        "Checksum enabled",
        "Unknown"
    )

    root_share_link = values.get(
        "Root share link ID",
        "Unknown"
    )

    root_user_code = values.get(
        "Root user code",
        "Unknown"
    )

    root_share_code = values.get(
        "Root share code",
        "Unknown"
    )

    popup = values.get(
        "Share popup settings",
        "Unknown"
    )

    click_play = "Unknown"
    dynamic_cover = "Unknown"
    follow_duration = "Unknown"
    profile_clickable = "Unknown"

    if popup != "Unknown":
        try:
            popup_data = json.loads(popup)

            click_play = popup_data.get(
                "click_empty_to_play",
                "Unknown"
            )

            dynamic_cover = popup_data.get(
                "dynamic_cover",
                "Unknown"
            )

            follow_duration = popup_data.get(
                "follow_to_play_duration",
                "Unknown"
            )

            profile_clickable = popup_data.get(
                "profile_clickable",
                "Unknown"
            )

        except (json.JSONDecodeError, TypeError):
            pass

    return f"""
════════════════════════════════════════════════════════════

ANALYSIS STATUS
The submitted TikTok URL has been processed and its embedded
sharing metadata has been extracted.

This report contains information encoded within the supplied
TikTok URL. It does not indicate access to a private TikTok
database, private account information, or an authenticated
account session.

────────────────────────────────────────────────────────────

CONTENT IDENTIFICATION

VIDEO CREATOR
The account associated with the submitted video is {creator}.
This is the public TikTok username appearing in the video's URL.

VIDEO IDENTIFIER
{video_id}

This numerical value identifies the specific TikTok video.

────────────────────────────────────────────────────────────

SHARING ENVIRONMENT

LANGUAGE
{language}

APPLICATION ID
{app_id}

SHARE SOURCE
{share_source}

UTM SOURCE
{utm_source}

CAMPAIGN
{campaign}

MEDIUM
{medium}

────────────────────────────────────────────────────────────

DISCOVERY CONTEXT

AUTHOR TYPE
{author_type}

ENTRY POINT
{entry_point}

BUSINESS CONTEXT
{business_context}

BUSINESS TRACKING
{business_tracking}

────────────────────────────────────────────────────────────

ACCOUNT IDENTIFIERS

TIKTOK USER ID
{user_id}

SECURE USER ID
{secure_id}

USER CODE
{user_code}

────────────────────────────────────────────────────────────

SHARING IDENTIFIERS

SHARE INSTANCE ID
{share_instance}

SHARE ITEM ID
{share_item}

SHARE LINK ID
{share_link}

────────────────────────────────────────────────────────────

TIME INFORMATION

TIMESTAMP
{timestamp}

UTC CONVERSION
{timestamp_date} UTC

────────────────────────────────────────────────────────────

SHARING INTERFACE

PREVIEW PB
{preview_pb}

PANEL SOURCE
{panel_source}

SP LEVEL
{sp_level}

SOCIAL SHARE TYPE
{social_type}

CHECKSUM ENABLED
{checksum}

────────────────────────────────────────────────────────────

ROOT SHARE INFORMATION

ROOT SHARE LINK ID
{root_share_link}

ROOT USER CODE
{root_user_code}

ROOT SHARE CODE
{root_share_code}

────────────────────────────────────────────────────────────

SHARE POPUP CONFIGURATION

CLICK EMPTY TO PLAY
{click_play}

DYNAMIC COVER
{dynamic_cover}

FOLLOW TO PLAY DURATION
{follow_duration}

PROFILE CLICKABLE
{profile_clickable}

These values are internal interface configuration flags and
should not be interpreted as private account information.

════════════════════════════════════════════════════════════"""


def send_txt_to_discord(text):
    if not WEBHOOK_URL:
        print("Discord webhook is not configured.")
        return

    if "PASTE_YOUR_NEW" in WEBHOOK_URL:
        print("Discord webhook is not configured.")
        return

    filename = "tiktok_metadata.txt"

    try:
        with tempfile.NamedTemporaryFile(
            mode="w",
            encoding="utf-8",
            suffix=".txt",
            delete=False
        ) as file:

            file.write(text)
            file_path = file.name

        with open(file_path, "rb") as file:
            response = requests.post(
                WEBHOOK_URL,
                data={
                    "content": "TikTok metadata report attached."
                },
                files={
                    "file": (
                        filename,
                        file,
                        "text/plain"
                    )
                },
                timeout=15
            )

        os.remove(file_path)

        if response.ok:
            print("TXT report uploaded to Discord.")
        else:
            print(
                f"Discord upload failed: "
                f"{response.status_code}"
            )

    except requests.RequestException as error:
        print(f"Discord webhook error: {error}")

    except OSError as error:
        print(f"Could not create TXT report: {error}")


def main():
    print("SICKALERT // TIKTOK METADATA ANALYSIS")
    print()

    url = input("TikTok URL: ").strip()

    if not url:
        print("No URL entered.")
        return

    print()
    print("Specialised [1]")
    print("Normal [2]")
    print()

    mode = input("Output mode: ").strip()

    while mode not in ("1", "2"):
        print("Please choose 1 or 2.")
        mode = input("Output mode: ").strip()

    # expand TikTok short links
    hostname = urlparse(url).hostname

    if hostname:
        hostname = hostname.lower()

    if hostname in (
        "vm.tiktok.com",
        "vt.tiktok.com"
    ):
        print()
        print("Expanding TikTok short link...")

        expanded_url = expand_tiktok_url(url)

        print()
        print("Expanded URL:")
        print(expanded_url)

        url = expanded_url

    # extract metadata
    fields = extract_metadata(url)

    # create selected report
    if mode == "1":
        output = specialized_output(fields)
    else:
        output = normal_output(fields)

    # print the complete report
    print()
    print(output)
    print()

    # upload the complete report as TXT
    send_txt_to_discord(output)


if __name__ == "__main__":
    main()
