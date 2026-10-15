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

# ===== 修复 CI 下 UnityFramework 的代码签名 =====
# store 签名脚本只把手动签名写进 App 目标;UnityFramework 是内嵌动态框架
# (Embed Frameworks + CodeSignOnCopy),而 Unity 导出时它仍是 Automatic + 空 identity。
# runner 上没有 Apple ID,自动签名无法解析,归档会失败(exit 65)。这里把它改成
# 手动 + Apple Distribution 身份;框架不需要 profile(App 的 profile 覆盖内嵌框架)。
post_install do |_installer|
  project = Xcodeproj::Project.open('Unity-iPhone.xcodeproj')
  framework = project.targets.find { |t| t.name == 'UnityFramework' }
  if framework
    framework.build_configurations.each do |config|
      config.build_settings['CODE_SIGN_STYLE'] = 'Manual'
      config.build_settings['CODE_SIGN_IDENTITY'] = 'Apple Distribution'
      config.build_settings['DEVELOPMENT_TEAM'] = 'N42X522L6D'
      config.build_settings.delete('PROVISIONING_PROFILE_SPECIFIER')
    end
    project.save
  end
end
