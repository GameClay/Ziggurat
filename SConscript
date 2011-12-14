import os

vars = Variables()

# Common build variables
vars.Add(EnumVariable('OS', 'Target OS', 'linux', allowed_values=('linux', 'win7', 'winxp', 'android', 'darwin', 'ios')))
vars.Add(EnumVariable('CPU', 'Target CPU', 'x86', allowed_values=('x86', 'x86-64', 'IA64', 'arm')))
vars.Add(EnumVariable('VARIANT', 'Build variant', 'debug', allowed_values=('debug', 'release')))
vars.Add(EnumVariable('MSVC_VERSION', 'MSVC compiler version - Windows', '9.0', allowed_values=('9.0', '10.0')))

# Standard variant directories
build_dir = 'build/${OS}/${CPU}/${VARIANT}'
vars.AddVariables(('OBJDIR', '', build_dir + '/obj'),
                  ('DISTDIR', '', '#' + build_dir + '/dist'))

env = Environment(variables = vars)
path = env['ENV']['PATH']

# Recreate the environment with the correct path
if env['OS'] == 'win7' or env['OS'] == 'winxp':
   if env['CPU'] == 'x86':
      env = Environment(variables = vars, TARGET_ARCH='x86', MSVC_VERSION='${MSVC_VERSION}', ENV={'PATH': path})
      print 'Building for 32 bit Windows'
   elif env['CPU'] == 'IA64':
      print 'Building for 64 bit Windows'
      env = Environment(variables = vars, TARGET_ARCH='x86_64', MSVC_VERSION='${MSVC_VERSION}', ENV={'PATH': path})
   else:
      print 'Windows CPU must be x86 or IA64'
      Exit()
elif env['OS'] == 'android':
   env = Environment(variables = vars, tools = ['gnulink', 'gcc', 'g++', 'ar', 'as'], ENV={'PATH': path})
else:
   env = Environment(variables = vars, ENV={'PATH': path})

Help(vars.GenerateHelpText(env))

# Validate build vars
if env['OS'] == 'linux':
   env['OS_GROUP'] = 'posix'
   env['OS_CONF'] = 'linux'
elif env['OS'] == 'win7' or env['OS'] == 'winxp':
   env['OS_GROUP'] = 'windows'
   env['OS_CONF'] = 'windows'
elif env['OS'] == 'android':
   env['OS_GROUP'] = 'posix'
   env['OS_CONF'] = 'android'
elif env['OS'] == 'darwin':
   env['OS_GROUP'] = 'posix'
   env['OS_CONF'] = 'darwin'
elif env['OS'] == 'ios':
   env['OS_GROUP'] = 'posix'
   env['OS_CONF'] = 'ios'
else:
   print 'Unsupported OS/CPU combination'
   Exit()

if env['VARIANT'] == 'release':
   env.Append(CPPDEFINES = 'NDEBUG')

# Read OS and CPU specific SConscript file
Export('env')
env.SConscript('conf/${OS_CONF}/${CPU}/SConscript')

Return('env')
