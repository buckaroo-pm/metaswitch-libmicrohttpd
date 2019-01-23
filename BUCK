load('//:buckaroo_macros.bzl', 'buckaroo_deps_from_package')
load('//:subdir_glob.bzl', 'subdir_glob')
load('//:extract.bzl', 'extract')

pthread = buckaroo_deps_from_package('github.com/buckaroo-pm/host-pthread')

genrule(
  name = 'configure',
  out = 'out',
  srcs = glob([
    'install-sh',
    'ltmain.sh',
    '*.in',
    'missing',
    'configure',
    'config.*',
    'contrib/**/*.in',
    'doc/**/*.in',
    'm4/**/*.in',
    'src/**/*.in',
    'src/**/*.rc',
    'w32/**/*.in',
    'w32/**/*.rc',
  ]),
  cmd = ' && '.join([
    'mkdir -p $OUT',
    'cd $OUT',
    'sh $SRCDIR/configure --srcdir=$SRCDIR --disable-doc --disable-examples --without-gnutls',
  ])
)

cxx_library(
  name = 'microhttpd',
  header_namespace = '',
  exported_headers = dict(
    subdir_glob([
      ('src/include', '**/*.h'),
    ]).items() + [
      ('MHD_config.h', extract(':configure', 'MHD_config.h')),
    ]
  ),
  srcs = glob([
    'src/microhttpd/**/*.c',
  ], exclude = glob([
    'src/microhttpd/**/*_https.c',
    'src/microhttpd/**/test_*.c',
  ])),
  platform_deps = [
    ('linux.*', pthread),
  ],
  visibility = [
    'PUBLIC',
  ],
)

prebuilt_cxx_library(
  name = 'magic',
  header_only = True,
  exported_linker_flags = [
    '-lmagic',
  ],
)

cxx_binary(
  name = 'demo',
  srcs = [
    'src/examples/demo.c',
  ],
  deps = [
    '//:microhttpd',
    ':magic',
  ],
)
