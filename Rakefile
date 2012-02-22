# encoding: UTF-8
require 'rake'
require 'systemu'

NAME = 'isodoc'
CLS  = NAME + '.cls'
DOC  = NAME + '.pdf'
SRC  = NAME + '.dtx'
INS  = NAME + '.ins'
EXAMPLES_SRC = %w{
  doc/accept.tex
  doc/invoice.tex
  doc/letter.tex
  doc/logoletter.tex
  doc/ntgletter.tex
}
EXAMPLES = EXAMPLES_SRC.map { |each| each.sub /\.tex$/, '.pdf' }
EXAMPLES_OTHER = %w{
  doc/isontg.sty
  doc/isowybo.sty
  doc/logostyle.sty
  doc/mystyle.sty
  doc/typo
  doc/ltxdoc.cfg
}
EXAMPLES_DATA = %w{
  doc/letter.txt
  doc/mystyle.txt
  doc/logostyle.txt
  doc/logoletter.txt
  doc/invoice.txt
  doc/accept.txt
  doc/logo1.pdf
  doc/logo2.pdf
}
BINARIES = %w{
  doc/acceptform.jpg
  doc/shiva_shakti.jpg
  doc/signw_marked.jpg
  doc/ntgcolor.png
  doc/signblue.png
  doc/signgrey.png
  doc/signws.png
  doc/chopinsc.afm
  doc/chopinsc.map
  doc/chopinsc.pfb
  doc/chopinsc.tfm
  doc/ChopinScript.otf
}

task :default => [CLS, DOC]

file CLS => [SRC, INS] do
  run ['tex', INS], :stdin => 'y'
end

file DOC => [SRC, *BINARIES, *EXAMPLES, *EXAMPLES_DATA] do
  run ['latexmk', SRC],
  :env => {'TEXINPUTS' => 'doc:'}
end

rule '.pdf' => [ proc{ |tn| tn.sub /\.pdf$/, '.tex' } ] do |t|
  opts = if /logoletter/ =~ t.name
    ['-e', '$pdflatex=q/xelatex %O %S/']
  else
    []
  end
  run ['latexmk', *opts, File.basename(t.source)],
    :cwd => File.join('.', File.dirname(t.name)),
    :env => {'TEXINPUTS' => '..:.:'}
end

FileList["doc/logo1.pdf", "doc/logo2.pdf"].each do |f|
  file f => 'doc/logoletter.pdf' do |t|
    run %W{pdfseparate -f 1 -l 2 logoletter.pdf logo%d.pdf},
        :cwd => 'doc'
  end
end

['.tex', '.sty'].each do |ext|
  rule '.txt' => [ proc{ |tn| tn.sub /\.txt$/, ext } ] do |t|
    File.open(t.name, 'w') do |io|
      run ['grep', '-v', '$\(Id\): ', t.source],
        :stdout => io
    end
  end
end

task :extract_binaries => 'doc/binaries.m64' do
  run %W{uudecode -o doc/binaries.zip doc/binaries.m64}
  run %W{unzip -d doc doc/binaries.zip}, :stdin => 'A'
end
FileList[*BINARIES].each do |f|  file f => :extract_binaries  end

# task :package_binaries => BINARIES

def run *args
  puts args.first.join ' '
  status, stdout, stderr = systemu *args
  puts stdout, stderr unless status.success?
end
