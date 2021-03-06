.. index::
   single: Process
   single: Components; Process

The Process Component
=====================

    The Process Component executes commands in sub-processes.

Installation
------------

You can install the component in many different ways:

* Use the official Git repository (https://github.com/symfony/Process);
* :doc:`Install it via Composer</components/using_components>` (``symfony/process`` on `Packagist`_).

Usage
-----

The :class:`Symfony\\Component\\Process\\Process` class allows you to execute
a command in a sub-process::

    use Symfony\Component\Process\Process;

    $process = new Process('ls -lsa');
    $process->setTimeout(3600);
    $process->run();
    if (!$process->isSuccessful()) {
        throw new \RuntimeException($process->getErrorOutput());
    }

    print $process->getOutput();

The :method:`Symfony\\Component\\Process\\Process::run` method takes care
of the subtle differences between the different platforms when executing the
command.

When executing a long running command (like rsync-ing files to a remote
server), you can give feedback to the end user in real-time by passing an
anonymous function to the
:method:`Symfony\\Component\\Process\\Process::run` method::

    use Symfony\Component\Process\Process;

    $process = new Process('ls -lsa');
    $process->run(function ($type, $buffer) {
        if ('err' === $type) {
            echo 'ERR > '.$buffer;
        } else {
            echo 'OUT > '.$buffer;
        }
    });

If you want to execute some PHP code in isolation, use the ``PhpProcess``
instead::

    use Symfony\Component\Process\PhpProcess;

    $process = new PhpProcess(<<<EOF
        <?php echo 'Hello World'; ?>
    EOF
    );
    $process->run();

.. _Packagist: https://packagist.org/packages/symfony/process