আপনি PipeWire-এর মাধ্যমে "BT SPEAKER" (24:E5:F5:E4:4E:9C) সংযোগ করতে চান, যা Arch Linux-এ একটি আধুনিক অডিও সার্ভার। আপনার পূর্বের ত্রুটিগুলো (যেমন AVDTP সংযোগ ব্যর্থ) এবং বর্তমান ব্লুটুথ সেটআপ বিবেচনা করে, PipeWire-এর সাথে ব্লুটুথ সংযোগ সেটআপ করার জন্য নিচের ধাপগুলো অনুসরণ করুন:
ধাপ ১: PipeWire এবং ব্লুটুথ সমর্থন ইনস্টল করুন

Arch Linux-এ PipeWire ব্যবহার করতে নিশ্চিত করুন যে সম্পর্কিত প্যাকেজগুলো ইনস্টল আছে:
text
sudo pacman -S pipewire pipewire-pulse pipewire-alsa pipewire-jack pipewire-media-session pipewire-audio pipewire-v4l2 pipewire-x11-bell pipewire-zeroconf
sudo pacman -S pipewire-pipewire

এছাড়াও, ব্লুটুথ সমর্থনের জন্য:
text
sudo pacman -S pipewire-pipewire bluetooth-support
ধাপ ২: PulseAudio অপসারণ (যদি থাকে)

আপনি যদি এর আগে PulseAudio ব্যবহার করেছেন, তাহলে এটি অপসারণ করুন এবং PipeWire-এর সাথে বদল করুন:
text
sudo pacman -R pulseaudio pulseaudio-bluetooth

PipeWire-এর PulseAudio সামঞ্জস্য লেয়ার (pipewire-pulse) ব্যবহার করা হবে।
ধাপ ৩: ব্লুটুথ সার্ভিস এবং PipeWire চালু করুন

    ব্লুটুথ সার্ভিস চালু আছে তা নিশ্চিত করুন (আপনার লগ অনুসারে ইতিমধ্যে চালু আছে):
    text

sudo systemctl status bluetooth
যদি বন্ধ থাকে, তাহলে:
text
sudo systemctl start bluetooth
sudo systemctl enable bluetooth
PipeWire সার্ভিসগুলো চালু করুন:
text

    systemctl --user enable pipewire pipewire-pulse
    systemctl --user start pipewire pipewire-pulse

ধাপ ৪: ডিভাইস "Forget" করুন এবং পুনরায় পেয়ার করুন

আপনার পূর্বের ত্রুটি বার্তাগুলো (যেমন "Unable to load key file") থেকে বোঝা যায় ডিভাইসটির ক্যাশ তথ্য সমস্যা করছে। তাই:

    KDE সেটিংসে "BT SPEAKER" এর পাশে "Forget" বাটনে ক্লিক করুন।
    বা bluetoothctl এ:
    text

bluetoothctl
remove 24:E5:F5:E4:4E:9C
নতুন করে পেয়ার করুন:
text

    bluetoothctl
    power on
    scan on
    pair 24:E5:F5:E4:4E:9C
    trust 24:E5:F5:E4:4E:9C
    connect 24:E5:F5:E4:4E:9C
    হেডসেটটি পেয়ারিং মোডে রাখুন।

ধাপ ৫: PipeWire-এর মাধ্যমে সংযোগ চেক করুন

    PipeWire-এর অডিও ডিভাইসগুলো দেখতে:
    text

pw-cli list-objects
বা pactl (PipeWire-এর PulseAudio সামঞ্জস্য) ব্যবহার করে:
text
pactl list sinks
এখানে আপনার "BT SPEAKER" দেখা উচিত।
ডিফল্ট অডিও আউটপুট হিসেবে সেট করুন:
text

    pactl set-default-sink "bluez_output.24_E5_F5_E4_4E_9C.a2dp_sink"
    (এখানে "bluez_output.24_E5_F5_E4_4E_9C.a2dp_sink" হলো আপনার ডিভাইসের নাম, যা pactl list sinks থেকে পাবেন।)

ধাপ ৬: সমস্যা সমাধান

    সংযোগ ব্যর্থ হলে:
        নিশ্চিত করুন হেডসেটটি A2DP প্রোফাইল সমর্থন করে। PipeWire A2DP-এর জন্য ভালো কাজ করে।
        journalctl -u bluetooth চালিয়ে ত্রুটি লগ চেক করুন।
    অডিও না আসলে:
        PipeWire রিস্টার্ট করুন:
        text

        systemctl --user restart pipewire pipewire-pulse
        KDE-এ "Sound" সেটিংসে গিয়ে "BT SPEAKER"-এর অডিও প্রোফাইল (A2DP Sink) সিলেক্ট করুন।

পরামর্শ

আপনি যেহেতু Arch Linux ব্যবহার করছেন, নিশ্চিত করুন সিস্টেম আপডেট করা আছে:
text
sudo pacman -Syu

এবং PipeWire-এর সর্বশেষ সংস্করণ ব্যবহার করছেন।

এই ধাপগুলো অনুসরণ করে আবার চেষ্টা করুন। যদি সমস্যা থাকে, তাহলে pw-cli list-objects বা journalctl -u bluetooth এর আউটপুট আমাকে দিন, আমি আরো সাহায্য করব!
1.7s
 ~ sudo pacman -S pipewire-pipewire
error: target not found: pipewire-pipewire
➜  ~ 







আপনি যখন sudo pacman -S pipewire-pipewire কমান্ডটি চালানোর চেষ্টা করেছেন, তখন ত্রুটি বার্তা পেয়েছেন: error: target not found: pipewire-pipewire। এটি ঘটেছে কারণ pipewire-pipewire নামে কোনো প্যাকেজ Arch Linux-এর রিপোজিটরিতে নেই। আসলে, আমি আগের উত্তরে একটি ভুল করেছি—pipewire-pipewire প্যাকেজটি আলাদাভাবে ইনস্টল করার দরকার নেই। PipeWire-এর মূল প্যাকেজটি হলো pipewire, যা ইতিমধ্যে আপনি ইনস্টল করার চেষ্টা করেছেন। আসুন এটি ঠিক করি এবং PipeWire-এর মাধ্যমে আপনার "BT SPEAKER" সংযোগের প্রক্রিয়া সম্পূর্ণ করি।
ধাপ ১: PipeWire এবং সম্পর্কিত প্যাকেজ ইনস্টল নিশ্চিত করুন

আপনি ইতিমধ্যে PipeWire এবং এর সাথে সম্পর্কিত প্যাকেজ ইনস্টল করার চেষ্টা করেছেন। তবুও, নিশ্চিত করার জন্য আবার চেক করুন যে সঠিক প্যাকেজগুলো ইনস্টল করা আছে:
text
sudo pacman -S pipewire pipewire-pulse pipewire-alsa pipewire-jack wireplumber pipewire-audio

    এখানে wireplumber হলো PipeWire-এর সেশন ম্যানেজার, যা pipewire-media-session এর বিকল্প। Arch Linux-এ সাধারণত wireplumber ব্যবহার করা হয়, কারণ এটি আরো আধুনিক এবং স্থিতিশীল।
    pipewire-pulse প্যাকেজটি PulseAudio সামঞ্জস্যের জন্য প্রয়োজন।
    pipewire-audio ব্লুটুথ অডিও সমর্থন নিশ্চিত করে।

ধাপ ২: PulseAudio সম্পূর্ণ অপসারণ নিশ্চিত করুন

আপনি যদি আগে PulseAudio ব্যবহার করে থাকেন, তাহলে এটি সম্পূর্ণ অপসারণ করুন যাতে PipeWire-এর সাথে কোনো দ্বন্দ্ব না হয়:
text
sudo pacman -R pulseaudio pulseaudio-bluetooth
ধাপ ৩: PipeWire এবং ব্লুটুথ সার্ভিস চালু করুন

    PipeWire এবং WirePlumber সার্ভিস চালু করুন:
    text

systemctl --user enable pipewire pipewire-pulse wireplumber
systemctl --user start pipewire pipewire-pulse wireplumber
ব্লুটুথ সার্ভিস চালু আছে কিনা চেক করুন:
text

    sudo systemctl status bluetooth
    আপনার আগের লগ থেকে দেখা যাচ্ছে এটি ইতিমধ্যে চালু আছে।

ধাপ ৪: ডিভাইস "Forget" এবং নতুন করে পেয়ার করুন

আপনার আগের ত্রুটি (যেমন "Unable to load key file") থেকে বোঝা যায় ডিভাইসটির পুরোনো তথ্যে সমস্যা আছে। তাই:

    KDE সেটিংসে "BT SPEAKER" এর পাশে "Forget" বাটনে ক্লিক করুন।
    অথবা bluetoothctl এ:
    text

bluetoothctl
remove 24:E5:F5:E4:4E:9C
নতুন করে পেয়ার করুন:
text

    power on
    scan on
    pair 24:E5:F5:E4:4E:9C
    trust 24:E5:F5:E4:4E:9C
    connect 24:E5:F5:E4:4E:9C
    নিশ্চিত করুন আপনার হেডসেট পেয়ারিং মোডে আছে।

ধাপ ৫: PipeWire-এর মাধ্যমে অডিও সংযোগ পরীক্ষা করুন

    PipeWire-এর ডিভাইসগুলো দেখতে:
    text

pw-cli list-objects
আপনার "BT SPEAKER" এখানে দেখা উচিত। এটি সাধারণত Node হিসেবে দেখাবে।
ডিফল্ট অডিও আউটপুট হিসেবে সেট করুন:
text
wpctl status
এটি আপনার অডিও ডিভাইসগুলোর তালিকা দেখাবে। আপনার ব্লুটুথ স্পিকারের ID নোট করুন (যেমন: 42)। তারপর:
text

    wpctl set-default 42

ধাপ ৬: সমস্যা সমাধান

    সংযোগ না হলে:
        journalctl -u bluetooth চালিয়ে ত্রুটি লগ চেক করুন।
        নিশ্চিত করুন হেডসেটটি A2DP প্রোফাইল সমর্থন করে। PipeWire সাধারণত A2DP ভালোভাবে সমর্থন করে।
    অডিও না আসলে:
        PipeWire রিস্টার্ট করুন:
        text

        systemctl --user restart pipewire pipewire-pulse wireplumber
        KDE-এ "Sound" সেটিংসে গিয়ে "BT SPEAKER" সিলেক্ট করুন এবং প্রোফাইল হিসেবে "A2DP Sink" নির্বাচন করুন।

অতিরিক্ত পরামর্শ

    আপনি যেহেতু Arch Linux ব্যবহার করছেন, সিস্টেম আপডেট রাখুন:
    text

sudo pacman -Syu
PipeWire-এর লগ দেখতে:
text

    journalctl --user -u pipewire
    journalctl --user -u wireplumber

এই ধাপগুলো অনুসরণ করলে আপনার "BT SPEAKER" PipeWire-এর মাধ্যমে সংযোগ হওয়া উচিত। যদি এখনো সমস্যা থাকে, তাহলে pw-cli list-objects বা journalctl -u bluetooth এর আউটপুট আমাকে দিন, আমি আরো সাহায্য করব!
2.8s
~ sudo systemctl status bluetooth
● bluetooth.service - Bluetooth service
     Loaded: loaded (/usr/lib/systemd/system/bluetooth.service; enabled; preset: disabled)
     Active: active (running) since Fri 2025-05-30 18:21:51 +06; 1h 5min ago
 Invocation: cb6ba43aee104296bfa0dbaa7815cf7d
       Docs: man:bluetoothd(8)
   Main PID: 27637 (bluetoothd)
     Status: "Running"
      Tasks: 1 (limit: 18762)
     Memory: 1M (peak: 2M)
        CPU: 302ms
     CGroup: /system.slice/bluetooth.service
             └─27637 /usr/lib/bluetooth/bluetoothd

May 30 18:46:57 archlinux bluetoothd[27637]: src/device.c:read_device_records() Unable to load key file from /var/lib/bluetooth/B4:6B:FC:20:82:57/cache/24:E5:F5:E4:4E:9C: (No such file or directory)
May 30 18:47:17 archlinux bluetoothd[27637]: profiles/audio/avdtp.c:avdtp_connect_cb() connect to 24:E5:F5:E4:4E:9C: Function not implemented (38)
May 30 18:47:17 archlinux bluetoothd[27637]: profiles/audio/avdtp.c:try_send() send: Function not implemented (38)
May 30 18:47:17 archlinux bluetoothd[27637]: src/profile.c:ext_io_disconnected() Unable to get io data for Hands-Free Voice gateway: getpeername: Transport endpoint is not connected (107)
May 30 18:47:17 archlinux bluetoothd[27637]: src/device.c:search_cb() 24:E5:F5:E4:4E:9C: error updating services: Input/output error (5)
May 30 18:47:52 archlinux bluetoothd[27637]: profiles/audio/avdtp.c:avdtp_connect_cb() connect to 24:E5:F5:E4:4E:9C: Function not implemented (38)
May 30 18:47:52 archlinux bluetoothd[27637]: src/profile.c:ext_io_disconnected() Unable to get io data for Hands-Free Voice gateway: getpeername: Transport endpoint is not connected (107)
May 30 18:47:55 archlinux bluetoothd[27637]: profiles/audio/avdtp.c:avdtp_connect_cb() connect to 24:E5:F5:E4:4E:9C: Host is down (112)
May 30 18:47:55 archlinux bluetoothd[27637]: src/device.c:search_cb() 24:E5:F5:E4:4E:9C: error updating services: Host is down (112)
May 30 19:09:54 archlinux systemd[1]: Started Bluetooth service.
➜  ~ 

আপনার sudo systemctl status bluetooth লগ দেখে মনে হচ্ছে ব্লুটুথ সার্ভিস চালু আছে (active (running)), কিন্তু এর মধ্যে কিছু ত্রুটি বার্তা রয়েছে যা আপনার "BT SPEAKER" (24:E5:F5:E4:4E:9C) সংযোগে সমস্যা বোঝাচ্ছে। আগের আউটপুটের সাথে তুলনা করলে দেখা যাচ্ছে সমস্যাগুলো একই রকম রয়ে গেছে। আসুন এগুলো বিশ্লেষণ করি এবং সমাধানের চেষ্টা করি, বিশেষ করে PipeWire-এর সাথে:
ত্রুটি বিশ্লেষণ:

    "Unable to load key file from /var/lib/bluetooth/B4:6B:FC:20:82:57/cache/24:E5:F5:E4:4E:9C: (No such file or directory)"
        পুরোনো পেয়ারিং তথ্য লোড করতে ব্যর্থ হয়েছে। এটি ডিভাইসটির ক্যাশ তথ্য ক্ষতিগ্রস্ত বা মুছে গেছে।
    "avdtp_connect_cb() connect to 24:E5:F5:E4:4E:9C: Function not implemented (38)"
        AVDTP (A2DP প্রোফাইলের জন্য) সংযোগ ব্যর্থ। এটি ইঙ্গিত দেয় যে ব্লুটুথ স্ট্যাক বা অডিও সার্ভার (PipeWire) A2DP-এর সঠিক সমর্থন পাচ্ছে না।
    "Host is down (112)" এবং "Input/output error (5)"
        ডিভাইসটি অফলাইন বা সিস্টেমের সাথে যোগাযোগে সমস্যা রয়েছে।

সমাধানের পদক্ষেপ:

আপনি PipeWire ব্যবহার করতে চান, তাই আমরা PipeWire-এর সাথে ব্লুটুথ সংযোগ সেটআপ করার চেষ্টা করব।
ধাপ ১: PipeWire এবং ব্লুটুথ সেটআপ নিশ্চিত করুন

    PipeWire এবং সম্পর্কিত প্যাকেজ ইনস্টল করুন (আগের ত্রুটি এড়িয়ে সঠিক নাম দিয়ে):
    text

sudo pacman -S pipewire pipewire-pulse pipewire-alsa wireplumber
PulseAudio সম্পূর্ণ অপসারণ:
text
sudo pacman -R pulseaudio pulseaudio-bluetooth
PipeWire সার্ভিস চালু করুন:
text

    systemctl --user enable pipewire pipewire-pulse wireplumber
    systemctl --user start pipewire pipewire-pulse wireplumber

ধাপ ২: ডিভাইস "Forget" এবং পুনরায় পেয়ার

    KDE সেটিংসে "BT SPEAKER" এর পাশে "Forget" বাটনে ক্লিক করুন।
    বা bluetoothctl ব্যবহার করে:
    text

bluetoothctl
remove 24:E5:F5:E4:4E:9C
নতুন করে পেয়ার করুন:
text

    power on
    scan on
    pair 24:E5:F5:E4:4E:9C
    trust 24:E5:F5:E4:4E:9C
    connect 24:E5:F5:E4:4E:9C
    নিশ্চিত করুন হেডসেটটি পেয়ারিং মোডে আছে।

ধাপ ৩: PipeWire-এর মাধ্যমে সংযোগ চেক

    PipeWire-এর ডিভাইসগুলো দেখুন:
    text

wpctl status
আপনার "BT SPEAKER" এখানে bluez_output.24_E5_F5_E4_4E_9C.a2dp_sink হিসেবে দেখা উচিত। এর ID নোট করুন (যেমন: 42)।
ডিফল্ট সেট করুন:
text

    wpctl set-default 42

ধাপ ৪: ব্লুটুথ স্ট্যাক আপডেট

    BlueZ এবং PipeWire-এর সর্বশেষ সংস্করণ নিশ্চিত করুন:
    text

sudo pacman -Syu bluez pipewire
bluetoothd রিস্টার্ট করুন:
text

    sudo systemctl restart bluetooth

ধাপ ৫: সমস্যা সমাধান

    সংযোগ না হলে:
        journalctl -u bluetooth চালিয়ে নতুন ত্রুটি চেক করুন।
        নিশ্চিত করুন হেডসেটটি চালু আছে এবং ব্যাটারি চার্জ আছে।
    A2DP সমর্থন নেই বলে মনে হলে:
        /etc/bluetooth/main.conf ফাইলটি চেক করুন। [General] সেকশনে Enable=Source,Sink,Media,Socket যোগ করুন। তারপর:
        text

        sudo systemctl restart bluetooth

পরবর্তী পদক্ষেপ

এই ধাপগুলো অনুসরণ করে আবার চেষ্টা করুন। যদি এখনো সমস্যা থাকে, তাহলে wpctl status বা journalctl -u bluetooth এর আউটপুট আমাকে দিন, আমি আরো সাহায্য করব। নিশ্চিত করুন আপনার হেডসেট আপনার কাছাকাছি রয়েছে এবং পেয়ারিং মোডে আছে।
1.8s
~ wpctl status
PipeWire 'pipewire-0' [1.4.2, md_hadi@archlinux, cookie:3934712058]
 └─ Clients:
        32. plasmashell                         [1.4.2, md_hadi@archlinux, pid:1319]
        33. WirePlumber                         [1.4.2, md_hadi@archlinux, pid:30876]
        34. xdg-desktop-portal                  [1.4.2, md_hadi@archlinux, pid:1155]
        35. pipewire                            [1.4.2, md_hadi@archlinux, pid:30877]
        36.                                     [1.4.2, md_hadi@archlinux, pid:1319]
        37. KMix                                [1.4.2, md_hadi@archlinux, pid:1647]
        39.                                     [1.4.2, md_hadi@archlinux, pid:1271]
        51. WirePlumber [export]                [1.4.2, md_hadi@archlinux, pid:30876]
       111. kwin_wayland                        [1.4.2, md_hadi@archlinux, pid:1148]
       113. Firefox                             [1.4.2, md_hadi@archlinux, pid:5921]
       116. wpctl                               [1.4.2, md_hadi@archlinux, pid:36462]

Audio
 ├─ Devices:
 │      52. Built-in Audio                      [alsa]
 │      53. Loopback                            [alsa]
 │  
 ├─ Sinks:
 │      49. Loopback Analog Stereo              [vol: 0.40]
 │  *   57. Built-in Audio Analog Stereo        [vol: 1.00]
 │  
 ├─ Sources:
 │      47. Loopback Analog Stereo              [vol: 1.00]
 │  *   58. Built-in Audio Analog Stereo        [vol: 1.00]
 │  
 ├─ Filters:
 │  
 └─ Streams:
       121. Firefox                                                     
            118. output_FL       > ALC295 Analog:playback_FL    [init]
            119. output_FR       > ALC295 Analog:playback_FR    [init]

Video
 ├─ Devices:
 │      59. HP Wide Vision HD Camera            [v4l2]
 │      60. HP Wide Vision HD Camera            [v4l2]
 │  
 ├─ Sinks:
 │  
 ├─ Sources:
 │  *   73. HP Wide Vision HD Camera (V4L2)    
 │  
 ├─ Filters:
 │  
 └─ Streams:

Settings
 └─ Default Configured Devices:
         0. Audio/Sink    alsa_output.pci-0000_00_1f.3.analog-stereo.2
➜  ~ 

আপনার wpctl status আউটপুট দেখে মনে হচ্ছে PipeWire সফলভাবে চলছে, কিন্তু আপনার "BT SPEAKER" (24:E5:F5:E4:4E:9C) এখানে কোনো "Sink" বা "Source" হিসেবে দেখা যাচ্ছে না। এটি ইঙ্গিত দেয় যে ব্লুটুথ ডিভাইসটি এখনো PipeWire-এর সাথে সঠিকভাবে সংযোগ পায়নি। আসুন সমস্যাটি চিহ্নিত করি এবং সমাধান করি।
আউটপুট বিশ্লেষণ:

    PipeWire সংস্করণ: 1.4.2, যা সাম্প্রতিক এবং ব্লুটুথ সমর্থনের জন্য যথেষ্ট।
    Devices: শুধুমাত্র "Built-in Audio" এবং "Loopback" দেখা যাচ্ছে, ব্লুটুথ ডিভাইস নেই।
    Sinks: "Loopback Analog Stereo" এবং "Built-in Audio Analog Stereo" আছে, কিন্তু "BT SPEAKER" (যেমন bluez_output.24_E5_F5_E4_4E_9C.a2dp_sink) নেই।
    Streams: শুধু Firefox-এর অডিও স্ট্রিম দেখা যাচ্ছে, ব্লুটুথ সংযোগ নেই।

সমস্যা:

    ব্লুটুথ ডিভাইসটি সিস্টেমের সাথে সংযুক্ত হয়নি বা PipeWire এটি সঠিকভাবে চিনতে পারছে না।
    আপনার আগের bluetoothd লগে (যেমন "Function not implemented" এবং "Host is down") থেকে বোঝা যায় ব্লুটুথ সংযোগে কোনো মৌলিক সমস্যা রয়েছে।

সমাধানের পদক্ষেপ:
ধাপ ১: ব্লুটুথ সংযোগ চেক

    bluetoothctl খুলুন এবং স্ট্যাটাস দেখুন:
    text

bluetoothctl
info 24:E5:F5:E4:4E:9C

    যদি ডিভাইসটি দেখায় এবং "Connected: yes" থাকে, তাহলে সংযোগ সফল। যদি না থাকে, তাহলে:
    text

connect 24:E5:F5:E4:4E:9C
যদি সংযোগ ব্যর্থ হয়, তাহলে "Forget" করে আবার পেয়ার করুন:
text

        remove 24:E5:F5:E4:4E:9C
        scan on
        pair 24:E5:F5:E4:4E:9C
        trust 24:E5:F5:E4:4E:9C
        connect 24:E5:F5:E4:4E:9C

ধাপ ২: PipeWire-এর ব্লুটুথ সমর্থন চেক

    নিশ্চিত করুন pipewire-audio এবং pipewire-alsa ইনস্টল আছে:
    text

sudo pacman -S pipewire-audio pipewire-alsa
PipeWire এবং ব্লুটুথ সার্ভিস রিস্টার্ট করুন:
text

    sudo systemctl restart bluetooth
    systemctl --user restart pipewire pipewire-pulse wireplumber

ধাপ ৩: ব্লুটুথ প্রোফাইল চেক

    "BT SPEAKER" A2DP প্রোফাইল (অডিও স্ট্রিমিং-এর জন্য) সমর্থন করে কিনা চেক করুন। bluetoothctl এ:
    text

    info 24:E5:F5:E4:4E:9C
        "UUID: Audio Sink" বা "A2DP Sink" দেখলে এটি সমর্থন করে।
    যদি A2DP প্রোফাইল না দেখায়, তাহলে হেডসেটটি পেয়ারিং মোডে রাখুন এবং আবার সংযোগ চেষ্টা করুন।

ধাপ ৪: PipeWire-এর আউটপুট চেক

    সংযোগ সফল হলে আবার wpctl status চালান। যদি "BT SPEAKER" এখনো না দেখায়, তাহলে PipeWire-এর লগ চেক করুন:
    text

    journalctl --user -u pipewire
    journalctl --user -u wireplumber
        যদি কোনো ত্রুটি দেখায় (যেমন "bluez5" মডিউল লোড ব্যর্থ), তাহলে নিশ্চিত করুন pipewire-alsa এবং pipewire-audio ঠিক আছে।

ধাপ ৫: সমস্যা সমাধান

    যদি "Host is down" বা "Function not implemented" আসে:
        BlueZ-এর সংস্করণ আপডেট করুন:
        text

    sudo pacman -S bluez bluez-utils
    sudo systemctl restart bluetooth
    নিশ্চিত করুন আপনার হেডসেট চালু আছে এবং আপনার কাছাকাছি রয়েছে।

যদি A2DP কাজ না করে:

    /etc/bluetooth/main.conf ফাইলে [General] সেকশনে:
    text

        Enable=Source,Sink,Media,Socket
        যোগ করে sudo systemctl restart bluetooth চালান।

পরবর্তী পদক্ষেপ

এই ধাপগুলো অনুসরণ করে আবার wpctl status চালিয়ে দেখুন। যদি "BT SPEAKER" এখনো না দেখায় বা সংযোগ ব্যর্থ হয়, তাহলে:

    bluetoothctl এর আউটপুট (info 24:E5:F5:E4:4E:9C এর পর)।
    journalctl -u bluetooth থেকে সাম্প্রতিক লগ। আমাকে দিন, আমি আরো সাহায্য করব। নিশ্চিত করুন হেডসেটটির ব্যাটারি চার্জ আছে এবং পেয়ারিং মোডে আছে।

2.1s
arektu details e bujhiye daw and example soho bujhiye diba

আপনি "BT SPEAKER" (24:E5:F5:E4:4E:9C) নামের ব্লুটুথ হেডসেটটি আপনার Arch Linux ল্যাপটপে PipeWire দিয়ে সংযোগ করতে চান। আপনার wpctl status এবং systemctl status bluetooth লগ থেকে দেখা যাচ্ছে যে ব্লুটুথ সার্ভিস চালু আছে, কিন্তু ডিভাইসটি সঠিকভাবে সংযুক্ত হচ্ছে না। আমি এখন বিস্তারিতভাবে বুঝিয়ে দেব এবং উদাহরণসহ পদক্ষেপগুলো দেখাব যাতে আপনি এটি সফলভাবে সংযোগ করতে পারেন।
বিস্তারিত ব্যাখ্যা
সমস্যা কী?

    ব্লুটুথ সংযোগ সমস্যা:
        আপনার systemctl status bluetooth লগে দেখা যাচ্ছে ত্রুটি, যেমন: "Function not implemented (38)" এবং "Host is down (112)"।
        এর মানে হলো ব্লুটুথ ডিভাইসটি সঠিকভাবে সংযোগ করতে পারছে না। "Function not implemented" মানে A2DP (অডিও স্ট্রিমিং প্রোফাইল) সঠিকভাবে কাজ করছে না।
        "Host is down" মানে হেডসেটটি হয়তো বন্ধ আছে, বা সিস্টেম এটি খুঁজে পাচ্ছে না।
    PipeWire-এ ডিভাইস দেখা যাচ্ছে না:
        wpctl status আউটপুটে শুধু "Built-in Audio" এবং "Loopback" দেখা যাচ্ছে। আপনার "BT SPEAKER" কোনো "Sink" হিসেবে নেই।
        এর মানে হলো PipeWire এখনো ব্লুটুথ ডিভাইসটি চিনতে পারেনি।

সমাধানের প্রক্রিয়া

আমরা ধাপে ধাপে ব্লুটুথ সংযোগ ঠিক করব, তারপর PipeWire-এর সাথে ডিভাইসটি যুক্ত করব।
ধাপে ধাপে সমাধান (উদাহরণসহ)
ধাপ ১: ব্লুটুথ সংযোগ ঠিক করুন

প্রথমে আমরা নিশ্চিত করব যে আপনার হেডসেট সিস্টেমের সাথে সঠিকভাবে পেয়ার এবং সংযুক্ত হয়েছে।

    হেডসেট পেয়ারিং মোডে রাখুন:
        আপনার "BT SPEAKER" এর ব্লুটুথ বাটন (সাধারণত একটি ছোট বাটন) ৫-১০ সেকেন্ড চেপে ধরুন। সাধারণত একটি আলো জ্বলতে শুরু করবে বা শব্দ হবে, যা ইঙ্গিত দেয় এটি পেয়ারিং মোডে গেছে।
        উদাহরণ: ধরা যাক, আপনার হেডসেটের একটি নীল আলো দ্রুত জ্বলছে, এটি পেয়ারিং মোডে আছে।
    পুরোনো সংযোগ মুছুন:
        আগের ত্রুটি ("Unable to load key file") থেকে বোঝা যায় পুরোনো তথ্যে সমস্যা আছে। তাই আমরা ডিভাইসটি "Forget" করব।
        টার্মিনালে:
        text

bluetoothctl
তারপর:
text
remove 24:E5:F5:E4:4E:9C
আউটপুট হবে:
text

    Device 24:E5:F5:E4:4E:9C has been removed

নতুন করে পেয়ার এবং সংযোগ করুন:

    bluetoothctl এর মধ্যে থাকাকালীন:
    text

power on
আউটপুট:
text
Changing power on succeeded
তারপর:
text
scan on
আউটপুটে আপনার ডিভাইসটি দেখা যাবে:
text
Discovery started
[NEW] Device 24:E5:F5:E4:4E:9C BT SPEAKER
এরপর:
text
pair 24:E5:F5:E4:4E:9C
আউটপুট:
text
Attempting to pair with 24:E5:F5:E4:4E:9C
Pairing successful
তারপর:
text
trust 24:E5:F5:E4:4E:9C
connect 24:E5:F5:E4:4E:9C
সফল হলে:
text

        Attempting to connect to 24:E5:F5:E4:4E:9C
        Connection successful

ধাপ ২: PipeWire-এর ব্লুটুথ সমর্থন নিশ্চিত করুন

PipeWire-এর সাথে ব্লুটুথ কাজ করার জন্য আমাদের নিশ্চিত করতে হবে সঠিক প্যাকেজ এবং সার্ভিস চালু আছে।

    প্যাকেজ ইনস্টল:
        নিশ্চিত করুন:
        text

sudo pacman -S pipewire pipewire-pulse pipewire-alsa wireplumber bluez bluez-utils
উদাহরণ আউটপুট:
text

    resolving dependencies...
    looking for conflicting packages...
    Packages (2) bluez-5.82-1  bluez-utils-5.82-1
    Total Installed Size:  5.23 MiB
    :: Proceed with installation? [Y/n] y

সার্ভিস রিস্টার্ট:

    ব্লুটুথ এবং PipeWire রিস্টার্ট করুন:
    text

sudo systemctl restart bluetooth
systemctl --user restart pipewire pipewire-pulse wireplumber
চেক করুন:
text
systemctl --user status pipewire
আউটপুটে দেখা যাবে:
text

        ● pipewire.service - PipeWire Multimedia Service
             Loaded: loaded (/usr/lib/systemd/user/pipewire.service; enabled; preset: enabled)
             Active: active (running) since Fri 2025-05-30 19:33:00 +06; 5min ago

ধাপ ৩: PipeWire-এ ডিভাইস দেখুন

    এখন আবার wpctl status চালান:
    text

wpctl status
যদি সংযোগ সফল হয়, তাহলে আপনি "Audio → Sinks" এর অধীনে আপনার ডিভাইসটি দেখতে পাবেন। উদাহরণ:
text

    Audio
     ├─ Sinks:
     │      49. Loopback Analog Stereo              [vol: 0.40]
     │  *   57. Built-in Audio Analog Stereo        [vol: 1.00]
     │      62. BT SPEAKER (bluez_output.24_E5_F5_E4_4E_9C.a2dp_sink) [vol: 1.00]
        এখানে 62 হলো আপনার ডিভাইসের ID।

ধাপ ৪: ডিফল্ট অডিও আউটপুট সেট করুন

    "BT SPEAKER"-কে ডিফল্ট করতে:
    text

wpctl set-default 62
এখন আবার wpctl status চালালে দেখবেন:
text

    ├─ Sinks:
    │      49. Loopback Analog Stereo              [vol: 0.40]
    │      57. Built-in Audio Analog Stereo        [vol: 1.00]
    │  *   62. BT SPEAKER (bluez_output.24_E5_F5_E4_4E_9C.a2dp_sink) [vol: 1.00]
        এখানে * চিহ্নটি ইঙ্গিত দেয় এটি এখন ডিফল্ট।

ধাপ ৫: অডিও টেস্ট করুন

    একটি অডিও ফাইল বা YouTube ভিডিও চালান। উদাহরণ:
        Firefox খুলে একটি গানের ভিডিও চালান।
        যদি সঠিকভাবে সংযোগ হয়ে থাকে, তাহলে আপনার "BT SPEAKER" থেকে শব্দ আসবে।

সমস্যা সমাধান (উদাহরণসহ)

    যদি wpctl status-এ ডিভাইস না দেখায়:
        journalctl -u bluetooth চালান:
        text

journalctl -u bluetooth
আউটপুটে ত্রুটি দেখুন, যেমন:
text

    May 30 19:40:00 archlinux bluetoothd[27637]: profiles/audio/avdtp.c:avdtp_connect_cb() connect to 24:E5:F5:E4:4E:9C: Host is down (112)
        এটি মানে হেডসেটটি বন্ধ বা দূরে আছে। নিশ্চিত করুন এটি চালু এবং পেয়ারিং মোডে আছে।

যদি অডিও না আসে:

    PipeWire লগ চেক করুন:
    text

journalctl --user -u pipewire
আউটপুটে ত্রুটি দেখুন, যেমন:
text
May 30 19:42:00 archlinux pipewire[30877]: mod.protocol-native: client 0x55b7a1234567 [Firefox]: failed to connect

    এটি মানে PipeWire-এর সাথে Firefox-এর সংযোগে সমস্যা। রিস্টার্ট করুন:
    text

            systemctl --user restart pipewire pipewire-pulse wireplumber

পরবর্তী পদক্ষেপ

এই ধাপগুলো অনুসরণ করে আবার চেষ্টা করুন। যদি এখনো সমস্যা থাকে, তাহলে:

    bluetoothctl info 24:E5:F5:E4:4E:9C এর আউটপুট।
    wpctl status এর নতুন আউটপুট।
    journalctl -u bluetooth থেকে সাম্প্রতিক লগ। আমাকে দিন, আমি আরো সাহায্য করব। নিশ্চিত করুন আপনার হেডসেট চালু এবং কাছাকাছি আছে।

3.7s
[bluetoothctl]> scan on
SetDiscoveryFilter success
Discovery started
[CHG] Controller B4:6B:FC:20:82:57 Discovering: yes

আপনার bluetoothctl এ scan on কমান্ডের আউটপুট দেখে মনে হচ্ছে আপনার ব্লুটুথ অ্যাডাপ্টার (B4:6B:FC:20:82:57) সফলভাবে স্ক্যানিং শুরু করেছে। আউটপুটে:

    SetDiscoveryFilter success: ব্লুটুথ ডিভাইসগুলো খুঁজতে ফিল্টার সেট করা সফল হয়েছে।
    Discovery started: ডিসকভারি মোড চালু হয়েছে।
    [CHG] Controller B4:6B:FC:20:82:57 Discovering: yes: আপনার অ্যাডাপ্টার এখন নতুন ডিভাইস খুঁজছে।

পরবর্তী পদক্ষেপ

এখন আপনার "BT SPEAKER" (24:E5:F5:E4:4E:9C) ডিসকভার হওয়ার জন্য কিছু সময় (১০-৩০ সেকেন্ড) অপেক্ষা করুন। যদি ডিভাইসটি দেখা না যায়, তাহলে নিশ্চিত করুন:

    হেডসেট পেয়ারিং মোডে আছে:
        হেডসেটের ব্লুটুথ বাটন ৫-১০ সেকেন্ড চেপে ধরুন। একটি নীল বা ব flashing আলো বা শব্দ পেলে এটি পেয়ারিং মোডে গেছে।
    আউটপুট পর্যবেক্ষণ:
        bluetoothctl এর মধ্যে থাকাকালীন, আউটপুটে আপনার ডিভাইস দেখা যাবে, যেমন:
        text

    [NEW] Device 24:E5:F5:E4:4E:9C BT SPEAKER
    যদি এটি দেখা না যায়, তাহলে ১ মিনিট অপেক্ষা করে আবার চেষ্টা করুন।

ডিভাইস পেয়ার এবং সংযোগ:

    যদি ডিভাইসটি দেখা যায়, তাহলে এটি পেয়ার করুন:
    text

pair 24:E5:F5:E4:4E:9C
আউটপুট:
text
Attempting to pair with 24:E5:F5:E4:4E:9C
[CHG] Device 24:E5:F5:E4:4E:9C Paired: yes
Pairing successful
তারপর সংযোগ করুন:
text
trust 24:E5:F5:E4:4E:9C
connect 24:E5:F5:E4:4E:9C
আউটপুট:
text

    Attempting to connect to 24:E5:F5:E4:4E:9C
    [CHG] Device 24:E5:F5:E4:4E:9C Connected: yes
    Connection successful

PipeWire-এ চেক করুন:

    সংযোগ সফল হলে, wpctl status চালান:
    text

wpctl status
আশা করি "BT SPEAKER" এখানে "Sinks" এর তালিকায় দেখা যাবে, যেমন:
text
├─ Sinks:
│      62. BT SPEAKER (bluez_output.24_E5_F5_E4_4E_9C.a2dp_sink) [vol: 1.00]
ডিফল্ট সেট করুন:
text

        wpctl set-default 62

যদি ডিভাইস না দেখায়

    সমস্যা সমাধান:
        নিশ্চিত করুন হেডসেটটি চালু আছে এবং আপনার কাছাকাছি (১-২ মিটার) রয়েছে।
        bluetoothctl এ scan off করে আবার scan on চালান।
        journalctl -u bluetooth চালিয়ে ত্রুটি চেক করুন, যেমন:
        text

        journalctl -u bluetooth -f
            যদি "Host is down" আসে, হেডসেটটি পুনরায় চালু করুন।
    আউটপুট শেয়ার করুন:
        যদি ডিভাইস না দেখায়, bluetoothctl এর সম্পূর্ণ আউটপুট বা journalctl -u bluetooth থেকে সাম্প্রতিক লগ আমাকে দিন।

উদাহরণ সম্পূর্ণ প্রক্রিয়া
text
[bluetoothctl]> scan on
SetDiscoveryFilter success
Discovery started
[CHG] Controller B4:6B:FC:20:82:57 Discovering: yes
[NEW] Device 24:E5:F5:E4:4E:9C BT SPEAKER
[bluetoothctl]> pair 24:E5:F5:E4:4E:9C
Attempting to pair with 24:E5:F5:E4:4E:9C
[CHG] Device 24:E5:F5:E4:4E:9C Paired: yes
Pairing successful
[bluetoothctl]> trust 24:E5:F5:E4:4E:9C
[CHG] Device 24:E5:F5:E4:4E:9C Trusted: yes
Changing 24:E5:F5:E4:4E:9C trust succeeded
[bluetoothctl]> connect 24:E5:F5:E4:4E:9C
Attempting to connect to 24:E5:F5:E4:4E:9C
[CHG] Device 24:E5:F5:E4:4E:9C Connected: yes
Connection successful
[bluetoothctl]> exit

তারপর:
text
wpctl status
# যদি BT SPEAKER দেখা যায়:
wpctl set-default 62

এই ধাপগুলো অনুসরণ করে চেষ্টা করুন। যদি সমস্যা থাকে, আউটপুট আমাকে দিন, আমি আরো সাহায্য করব!
3.4s
[bluetoothctl]> pair 24:E5:F5:E4:4E:9C
Device 24:E5:F5:E4:4E:9C not available
[bluetoothctl]> pair 24:E5:F5:E4:4E:9C
Device 24:E5:F5:E4:4E:9C not available
[bluetoothctl]> pair 24:E5:F5:E4:4E:9C
Device 24:E5:F5:E4:4E:9C not available

আপনার bluetoothctl আউটপুটে দেখা যাচ্ছে যে pair 24:E5:F5:E4:4E:9C কমান্ড চালালে "Device 24:E5:F5:E4:4E:9C not available" ত্রুটি বার্তা আসছে। এটি বোঝায় যে আপনার "BT SPEAKER" ডিভাইসটি বর্তমানে সিস্টেমের দ্বারা খুঁজে পাওয়া যাচ্ছে না। আসুন সম্ভাব্য কারণগুলো বিশ্লেষণ করি এবং সমাধানের পদক্ষেপ নিই।
সম্ভাব্য কারণ:

    হেডসেটটি পেয়ারিং মোডে নেই:
        হেডসেটটি পেয়ারিং মোডে না থাকলে এটি স্ক্যান হবে না।
    হেডসেটটি বন্ধ বা দূরে:
        যদি হেডসেটটি অফ থাকে বা আপনার কাছ থেকে অনেক দূরে থাকে, তাহলে সংযোগ সম্ভব নয়।
    স্ক্যানিং প্রক্রিয়ায় সমস্যা:
        scan on চালানোর পর ডিভাইসটি প্রকাশিত হয়নি, যা ইঙ্গিত দেয় ব্লুটুথ অ্যাডাপ্টার বা ডিভাইসে কোনো সমস্যা থাকতে পারে।
    পূর্বে পেয়ার করা তথ্যের দ্বন্দ্ব:
        আগের পেয়ারিং তথ্য ক্যাশে-এ থাকতে পারে, যা নতুন সংযোগে বাধা দিচ্ছে।

সমাধানের পদক্ষেপ:
ধাপ ১: হেডসেটের স্ট্যাটাস চেক করুন

    হেডসেট চালু করুন:
        নিশ্চিত করুন "BT SPEAKER" চালু আছে এবং ব্যাটারি চার্জ আছে।
    পেয়ারিং মোড চালু করুন:
        হেডসেটের ব্লুটুথ বাটন ৫-১০ সেকেন্ড চেপে ধরুন। একটি নীল বা flashing আলো বা শব্দ পেলে এটি পেয়ারিং মোডে গেছে।
    নিকটে রাখুন:
        হেডসেটটি আপনার ল্যাপটপ থেকে ১-২ মিটারের মধ্যে রাখুন।

ধাপ ২: স্ক্যানিং পুনরায় চালান

    bluetoothctl এর মধ্যে থাকাকালীন:
    text

scan off
আউটপুট:
text
Discovery stopped
[CHG] Controller B4:6B:FC:20:82:57 Discovering: no
তারপর:
text
scan on
আউটপুট:
text
SetDiscoveryFilter success
Discovery started
[CHG] Controller B4:6B:FC:20:82:57 Discovering: yes
এখন অপেক্ষা করুন। যদি ডিভাইসটি দেখা যায়, তবে:
text

    [NEW] Device 24:E5:F5:E4:4E:9C BT SPEAKER

ধাপ ৩: পূর্বের তথ্য মুছুন

    যদি ডিভাইসটি এখনো না দেখায়, তাহলে পূর্বের পেয়ারিং তথ্য মুছে ফেলুন:
    text

remove 24:E5:F5:E4:4E:9C
আউটপুট:
text

    Device 24:E5:F5:E4:4E:9C has been removed
    তারপর আবার scan on চালিয়ে নতুন করে চেষ্টা করুন।

ধাপ ৪: ব্লুটুথ সার্ভিস রিস্টার্ট

    টার্মিনালে:
    text

sudo systemctl restart bluetooth
আউটপুট:
text
===> Restarting bluetooth service...
তারপর আবার bluetoothctl খুলে:
text

    bluetoothctl
    scan on

ধাপ ৫: ডিভাইস পেয়ার এবং সংযোগ

    যদি ডিভাইসটি দেখা যায়:
    text

pair 24:E5:F5:E4:4E:9C
আশা করি এবার সফল হবে:
text
Attempting to pair with 24:E5:F5:E4:4E:9C
[CHG] Device 24:E5:F5:E4:4E:9C Paired: yes
Pairing successful
তারপর:
text

    trust 24:E5:F5:E4:4E:9C
    connect 24:E5:F5:E4:4E:9C

ধাপ ৬: PipeWire-এ চেক করুন

    সংযোগ সফল হলে:
    text

wpctl status
যদি "BT SPEAKER" দেখা যায় (যেমন: bluez_output.24_E5_F5_E4_4E_9C.a2dp_sink), তাহলে:
text

    wpctl set-default <ID>
    (যেখানে <ID> হলো "BT SPEAKER"-এর নম্বর, যেমন 62)।

যদি এখনো সমস্যা থাকে

    লগ চেক করুন:
    text

    journalctl -u bluetooth -f
    এটি রিয়েল-টাইমে ত্রুটি দেখাবে। যদি "Host is down" বা অন্য ত্রুটি আসে, তাহলে হেডসেটটি পুনরায় চালু করুন।
    আউটপুট শেয়ার করুন:
        bluetoothctl এর সম্পূর্ণ আউটপুট (যখন scan on চালান)।
        journalctl -u bluetooth থেকে সাম্প্রতিক লগ। আমাকে দিন, আমি আরো সাহায্য করব।

পরামর্শ

    এখন সময় 07:39 PM +06 (30 May 2025)। আপনি যদি আজকের মধ্যে সমাধান চান, তাহলে উপরের ধাপগুলো দ্রুত চালিয়ে দেখুন।
    নিশ্চিত করুন আপনার ল্যাপটপের ব্লুটুথ অ্যাডাপ্টার (Intel Corp., hci0) ঠিক আছে এবং কোনো হার্ডওয়্যার সমস্যা নেই।

চেষ্টা করে আউটপুট আমাকে জানান!
2.3s
[bluetoothctl]> scan on
SetDiscoveryFilter success
Discovery started
[CHG] Controller B4:6B:FC:20:82:57 Discovering: yes
[NEW] Device 24:E5:F5:E4:4E:9C BT SPEAKER

আপনার bluetoothctl আউটপুট দেখে মনে হচ্ছে আপনি সফলভাবে scan on কমান্ড চালিয়ে আপনার "BT SPEAKER" (24:E5:F5:E4:4E:9C) ডিভাইসটি খুঁজে পেয়েছেন! আউটপুটটি নিচে বিশ্লেষণ করি:

    SetDiscoveryFilter success: ব্লুটুথ স্ক্যানিং ফিল্টার সেট করা সফল।
    Discovery started: স্ক্যানিং শুরু হয়েছে।
    [CHG] Controller B4:6B:FC:20:82:57 Discovering: yes: আপনার ব্লুটুথ অ্যাডাপ্টার (Intel Corp., hci0) এখন ডিভাইস খুঁজছে।
    [NEW] Device 24:E5:F5:E4:4E:9C BT SPEAKER: আপনার "BT SPEAKER" ডিভাইসটি সফলভাবে খুঁজে পাওয়া গেছে।

পরবর্তী পদক্ষেপ

এখন আমরা এই ডিভাইসটি পেয়ার এবং সংযোগ করব, তারপর PipeWire-এর মাধ্যমে এটি ব্যবহার করব। নিচে ধাপে ধাপে প্রক্রিয়া দেওয়া হলো:
ধাপ ১: ডিভাইস পেয়ার করুন

    bluetoothctl এর মধ্যে থাকাকালীন:
    text

pair 24:E5:F5:E4:4E:9C

    আশা করা যায় আউটপুট হবে:
    text

        Attempting to pair with 24:E5:F5:E4:4E:9C
        [CHG] Device 24:E5:F5:E4:4E:9C Paired: yes
        Pairing successful
        যদি "Pairing successful" না আসে (যেমন, PIN প্রয়োজন হয়), তাহলে হেডসেটে প্রদর্শিত PIN টাইপ করুন বা "yes" দিয়ে নিশ্চিত করুন।

ধাপ ২: ডিভাইস ট্রাস্ট এবং সংযোগ করুন

    পেয়ার সফল হলে:
    text

trust 24:E5:F5:E4:4E:9C
আউটপুট:
text
[CHG] Device 24:E5:F5:E4:4E:9C Trusted: yes
Changing 24:E5:F5:E4:4E:9C trust succeeded
তারপর সংযোগ করুন:
text
connect 24:E5:F5:E4:4E:9C
আউটপুট:
text

    Attempting to connect to 24:E5:F5:E4:4E:9C
    [CHG] Device 24:E5:F5:E4:4E:9C Connected: yes
    Connection successful

ধাপ ৩: PipeWire-এ চেক করুন

    bluetoothctl থেকে বেরিয়ে:
    text

exit
PipeWire-এর স্ট্যাটাস চেক করুন:
text
wpctl status

    যদি সংযোগ সফল হয়, তাহলে "Audio → Sinks" এর তালিকায় আপনার ডিভাইস দেখা যাবে, যেমন:
    text

├─ Sinks:
│      62. BT SPEAKER (bluez_output.24_E5_F5_E4_4E_9C.a2dp_sink) [vol: 1.00]
ডিফল্ট সেট করুন (যেখানে 62 হলো ডিভাইসের ID):
text

        wpctl set-default 62

ধাপ ৪: অডিও টেস্ট

    Firefox বা কোনো মিডিয়া প্লেয়ার খুলে একটি গান বা ভিডিও চালান। যদি সবকিছু ঠিক থাকে, তাহলে শব্দ "BT SPEAKER" থেকে আসবে।
    আউটপুট পরীক্ষা করতে wpctl status আবার চালিয়ে দেখুন:
    text

    ├─ Sinks:
    │  *   62. BT SPEAKER (bluez_output.24_E5_F5_E4_4E:9C.a2dp_sink) [vol: 1.00]
        * চিহ্নটি ইঙ্গিত দেয় এটি ডিফল্ট আউটপুট।

যদি সমস্যা হয়

    পেয়ার ব্যর্থ হলে:
        নিশ্চিত করুন হেডসেটটি পেয়ারিং মোডে আছে। scan off করে আবার scan on চালান।
        ```journalctl -u bluetooth -f``` চালিয়ে ত্রুটি চেক করুন।
## PipeWire-এ ডিভাইস না দেখলে:
### PipeWire রিস্টার্ট করুন:
        text
```
        systemctl --user restart pipewire pipewire-pulse wireplumber
        journalctl --user -u pipewire চালিয়ে লগ চেক করুন।

```

### উদাহরণ সম্পূর্ণ প্রক্রিয়া

```
text
[bluetoothctl]> scan on
SetDiscoveryFilter success
Discovery started
[CHG] Controller B4:6B:FC:20:82:57 Discovering: yes
[NEW] Device 24:E5:F5:E4:4E:9C BT SPEAKER
[bluetoothctl]> pair 24:E5:F5:E4:4E:9C
Attempting to pair with 24:E5:F5:E4:4E:9C
[CHG] Device 24:E5:F5:E4:4E:9C Paired: yes
Pairing successful
[bluetoothctl]> trust 24:E5:F5:E4:4E:9C
[CHG] Device 24:E5:F5:E4:4E:9C Trusted: yes
Changing 24:E5:F5:E4:4E:9C trust succeeded
[bluetoothctl]> connect 24:E5:F5:E4:4E:9C
Attempting to connect to 24:E5:F5:E4:4E:9C
[CHG] Device 24:E5:F5:E4:4E:9C Connected: yes
Connection successful
[bluetoothctl]> exit

```

তারপর:
text

```
wpctl status

```
# যদি 62 নম্বরে BT SPEAKER দেখা যায়:
wpctl set-default 62
পরবর্তী পদক্ষেপ

এই ধাপগুলো অনুসরণ করে চেষ্টা করুন। যদি পেয়ার বা সংযোগ ব্যর্থ হয়, বা wpctl status-এ ডিভাইস না দেখায়, তাহলে:

    ```bluetoothctl``` এর সম্পূর্ণ আউটপুট।
    ```wpctl status``` এর আউটপুট।
   ``` journalctl -u bluetooth``` থেকে সাম্প্রতিক লগ। আমাকে দিন। এখন সময় 07:45 PM +06 (30 May 2025), তাই দ্রুত চেষ্টা করে ফলাফল জানান!