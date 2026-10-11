# 分块拼接 shim:libiPhone-lib.a(221298936 字节)因超过 GitHub 单文件 100MB 限制,
# 被切成 3 个 <100MB 的块提交。runner 检测到本 Podfile 会执行 pod install,
# 在此 pre_install 阶段把三块 cat 拼回原文件,再编译。
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

target 'Unity-iPhone' do
end
