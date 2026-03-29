project('sway-systemd', [],
  meson_version: '>= 0.51',
  license: 'MIT',
)

enabled = get_option('autoload-configs')
configs = {
  'config.d/10-systemd-session.conf.in':        true,
  'config.d/10-systemd-cgroups.conf.in':        enabled.contains('all') or enabled.contains('cgroups'),
  'config.d/95-system-keyboard-config.conf.in': enabled.contains('all') or enabled.contains('locale1'),
}

scripts = [
  'src/session.sh',
  'src/assign-cgroups.py',
  'src/wait-sni-ready',
  'src/locale1-xkb-config',
]

unit_files = [
  'units/sway-session.target',
]
unit_template = [
  'units/wait-sni-ready.service.in'
]

systemd = dependency('systemd')
conf_dir = get_option('sysconfdir') / 'sway' / 'config.d'
data_dir = get_option('datadir') / meson.project_name()
# must be absolute path for configuration_data
exec_dir = get_option('prefix') / get_option('libexecdir') / meson.project_name()

install_data(
  scripts,
  install_dir: exec_dir,
  install_mode: 'rwxr-xr-x',
)

install_data(
  unit_files,
  install_dir: systemd.get_variable(pkgconfig: 'systemduserunitdir'),
)

conf_data = configuration_data()
conf_data.set('execdir', exec_dir)

foreach config, enabled : configs
  configure_file(
    configuration: conf_data,
    input: config,
    output: '@BASENAME@',
    install_dir: enabled ? conf_dir : data_dir,
  )
endforeach

foreach template: unit_template
  configure_file(
    configuration: conf_data,
    input: template,
    output: '@BASENAME@',
    install_dir: systemd.get_variable(pkgconfig: 'systemduserunitdir'),
  )
endforeach