import os, sys, glob

## Prefic for install(ed) files
prefix="/usr/local"
#if not os.path.isdir(prefix):
#  print('[ERROR] Cannot find \'prefix\' directory, aka {:}; aborting'.format(prefix), file=sys.stderr)
#  sys.exit(1)

## Library version
lib_version="0.1.0"
## Library name
lib_name="celgeo"
## Include dir (following prefix) if any
inc_dir="celgeo"
## the rootdir of the project
root_dir=os.path.abspath(os.getcwd())

## get number of CPUs and use for parallel builds
num_cpu = int(os.environ.get('NUM_CPU', 2))
SetOption('num_jobs', num_cpu)
print("running with -j %s" % GetOption('num_jobs'))

## choose compiler from command line, e.g. '--cxx=clang++'
AddOption('--cxx',
          dest='cxx',
          type='string',
          nargs=1,
          action='store',
          metavar='CXX',
          help='C++ Compiler',
          default=None)

## choose compiler from command line, e.g. '--std=20'
AddOption('--std',
          dest='std',
          type='string',
          nargs=1,
          action='store',
          metavar='STD',
          help='C++ Standard [11/14/17/20]',
          default='17')

## Source files (for lib)
lib_src_files = glob.glob(r"src/*.cpp")

## Headers (for lib)
hdr_src_files = glob.glob(r"src/*.hpp")

## Environments ...
denv = Environment(CXXFLAGS='-g -pg -Wall -Wextra -Werror -pedantic -W -Wshadow -Winline -Wdisabled-optimization -DDEBUG')
penv = Environment(CXXFLAGS='-Wall -Wextra -Werror -pedantic -W -Wshadow -Winline -O2 -march=native')

## Command line arguments ...
debug = ARGUMENTS.get('debug', 0)

## Construct the build enviroment
env = denv.Clone() if int(debug) else penv.Clone()

## What compiler should we be using ?
if GetOption('cxx') is not None: env['CXX'] = GetOption('cxx')

## Set the C++ standard
cxxstd = GetOption('std')
env.Append(CXXFLAGS=' --std=c++{}'.format(cxxstd))

## (shared) library ...
vlib = env.SharedLibrary(source=lib_src_files, target=lib_name, CPPPATH=['.'], SHLIBVERSION=lib_version)

## Build ....
env.Program(source='test/sofa_examples.cpp', target='bin/sofa_examples.out',
             LIBS=vlib, LIBPATH='.', CPPPATH=['src/'])
#            LIBS=vlib+['geodesy', 'datetime'], LIBPATH='.', CPPPATH=['src/'])
env.Alias(target='install', source=env.Install(dir=os.path.join(prefix, 'include', inc_dir), source=hdr_src_files))
env.Alias(target='install', source=env.InstallVersionedLib(dir=os.path.join(prefix, 'lib'), source=vlib))

## Tests ...
#tests_sources = glob.glob(r"test/*.cpp")
#env.Append(RPATH=root_dir)
#for tsource in tests_sources:
#  ttarget = tsource.replace('_', '-').replace('.cpp', '.out')
#  env.Program(target=ttarget, source=tsource, CPPPATH='src/', LIBS=vlib+['geodesy', 'datetime'], LIBPATH='.')
