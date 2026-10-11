source 'https://github.com/CocoaPods/Specs'
source 'https://cdn.cocoapods.org/'

platform :ios, '13.0'

# ===== 分块拼接 shim(恢复)=====
# libiPhone-lib.a(221298936 字节)超过 GitHub 单文件 100MB 限制,被切成 3 个块提交。
# runner 检测到本 Podfile 会执行 pod install,在此 pre_install 阶段把三块拼回原文件再编译。
CHUNKS = %w[
  Libraries/libiPhone-lib.a.part0
  Libraries/libiPhone-lib.a.part1
  Libraries/libiPhone-lib.a.part2
]
OUT = 'Libraries/libiPhone-lib.a'
SIZE = 221298936

pre_install do |_installer|
  if !File.exist?(OUT) || File.size(OUT) != SIZE
    File.open(OUT, 'wb') { |f| CHUNKS.each { |c| f << File.binread(c) } }
  end
  abort("libiPhone-lib.a reassembly failed (size #{File.size(OUT)} != #{SIZE})") if File.size(OUT) != SIZE
end

# ===== Google Mobile Ads Unity 插件(com.google.ads.mobile)原生依赖 =====
# unity-plugin-library.a 里的 GADU*/UMP 代码引用了这些原生符号,必须在 UnityFramework 链接。
target 'UnityFramework' do
  pod 'Google-Mobile-Ads-SDK', '~> 12.14'
  pod 'GoogleUserMessagingPlatform', '3.1.0'
end
target 'Unity-iPhone' do
end
use_frameworks! :linkage => :static
