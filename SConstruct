import os, sys, re, string
sys.path.append('../../framework')
import bldutil

progs = ''' 
'''

# anisglfd2pml

ccprogs = ''' oneapp '''
# anisglfdcx2_7 anisglfdcz2_7 wlslfdcx2tw2 wlslfdcz2tw2

#pyprogs = 'fft'

try:  # distributed version
    Import('env root pkgdir bindir libdir incdir')
    env = env.Clone()
except: # local version
    env = bldutil.Debug()
    root = None
    SConscript('../lexing/SConstruct')

src = Glob('[a-z]*.c')

dynpre = env.get('DYNLIB','') ##
 
libs = [dynpre+'rsf']+env.get('LIBS',[]) ##
dlibs = ['drsf']+env.get('LIBS',[])   ##

env.Prepend(CPPPATH=['../../include'],
            LIBPATH=['../../lib'],
            LIBS=[env.get('DYNLIB','')+'rsf'])

fftw = env.get('FFTW')
if fftw:
    env.Prepend(CPPDEFINES=['SF_HAS_FFTW'])

for source in src:
    inc = env.RSF_Include(source,prefix='')
    obj = env.StaticObject(source)
    env.Depends(obj,inc)

mains = Split(progs)
for prog in mains:
    sources = ['M' + prog]
    bldutil.depends(env,sources,'M'+prog)
    env.StaticObject('M'+prog+'.c')
    prog = env.Program(prog,map(lambda x: x + '.o',sources),
                       LIBS=libs)
    if root:
        env.Install(bindir,prog)

if 'c++' in env.get('API',[]):
    lapack = env.get('LAPACK')
else:
    lapack = None

if lapack:
    libsxx = [env.get('DYNLIB','')+'rsf++','vecmatop']
    if not isinstance(lapack,bool):
        libsxx.extend(lapack)
    env.Prepend(LIBS=libsxx)
 

ccmains = Split(ccprogs)
for prog in ccmains:
    sources = ['M' + prog]
    if lapack:
        prog = env.Program(prog,map(lambda x: x + '.cc',sources))
    else:
        prog = env.RSF_Place('sf'+prog,None,var='LAPACK',package='lapack')
    if root:
        env.Install(bindir,prog)

######################################################################
# SELF-DOCUMENTATION
######################################################################
if root:
    user = os.path.basename(os.getcwd())
    main = 'sf%s.py' % user
    
    docs = map(lambda prog: env.Doc(prog,'M' + prog),mains) +  \
           map(lambda prog: env.Doc(prog,'M%s.cc' % prog,lang='c++'),ccmains)

    env.Depends(docs,'#/framework/rsf/doc.py')	
    doc = env.RSF_Docmerge(main,docs)
    env.Install(pkgdir,doc)

