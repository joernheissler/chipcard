Accessing Chip Cards from python
================================
This project provides an async (`anyio <https://github.com/agronholm/anyio>`__) interface
to communicate with the `pcscd <https://pcsclite.apdu.fr/>`__ process.

It's a 100% python implementation, i.e. no C code or C dependencies are involved.

Requirements
------------
* pcscd (``apt install pcscd``)
* Some async event loop, e.g. trio (``venv/bin/pip install trio``)
* A smart card that is reachable through pcscd

Example
-------
Code to generate a signature on OpenPGP card:

.. code-block:: python

    from anyio import run
    from contextlib import suppress
    from chipcard import connect_chipcard
    from chipcard.card.openpgp import OpenPgpCard

    USER_PW = b"123456"

    async def main():
        async with connect_chipcard(OpenPgpCard) as card, card.card.transaction():
            await card.select_openpgp()
            await card.unlock_pw1_sign(USER_PW)
            sig = await card.compute_digital_signature(b"Hello, World!")
            print(sig.hex())

    with suppress(KeyboardInterrupt):
        run(main, backend="trio")


Development status
------------------
It's currently more of a PoC. API needs to be cleaned up a lot and will
probably change in incompatible ways.

Vital features like Secure Messaging are missing entirely.
And eventually high level APIs for existing smart cards should be implemented.
